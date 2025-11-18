
# Prompt for Codex (Objective-C UIWebView → WKWebView Migration, Autorewrite)

**Role & Goal**
You are a senior iOS engineer. Rewrite an existing Objective-C iOS codebase to remove all usage of `UIWebView` and migrate to `WKWebView`, producing clean, compilable code and reviewable diffs. Preserve behavior; where exact parity isn’t possible, implement the closest modern equivalent and note it.

**Project Assumptions**

* Language: Objective-C (may include .m, .mm, .h, categories, and nibs/storyboards).
* Build system: Xcode project or workspace.
* Target iOS: iOS 11+ (use availability guards if needed).
* You may not run the project; ensure changes compile and include comments for any required manual storyboard edits.

---

## 1) Migration Plan (execute in this order)

1. **Inventory & Search**

   * Find **all references** to `UIWebView`, `UIWebViewDelegate`, `stringByEvaluatingJavaScriptFromString`, and any categories or wrappers around `UIWebView`.
   * Search third-party code (Pods, vendor libs) for the literal string `"UIWebView"`; propose upgrades or replacements for offending pods/libraries.
   * Output a file list of impacted sources.

2. **API Mapping (Objective-C)**
   Apply these transformations everywhere (headers & impl files):

   | Legacy (`UIWebView`)                           | Modern (`WKWebView`)                                                                                                      |
   | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
   | `UIWebView *webView;`                          | `WKWebView *webView;` (via `WKWebViewConfiguration *config`)                                                              |
   | `id<UIWebViewDelegate>`                        | `WKNavigationDelegate`, `WKUIDelegate`                                                                                    |
   | `loadRequest:`                                 | `loadRequest:` (same signature on `WKWebView`)                                                                            |
   | `stringByEvaluatingJavaScriptFromString:`      | `-evaluateJavaScript:completionHandler:`                                                                                  |
   | `scalesPageToFit`                              | No direct equivalent → rely on `<meta name="viewport">` or injected JS/CSS; document this                                 |
   | `dataDetectorTypes`                            | `configuration.dataDetectorTypes` (iOS 10+)                                                                               |
   | `delegate` callbacks                           | Map:                                                                                                                      |
   | `webViewDidStartLoad:`                         | `webView:didStartProvisionalNavigation:`                                                                                  |
   | `webViewDidFinishLoad:`                        | `webView:didFinishNavigation:`                                                                                            |
   | `didFailLoadWithError:`                        | `webView:didFailNavigation:withError:` and `didFailProvisionalNavigation`                                                 |
   | `shouldStartLoadWithRequest:navigationType:`   | `webView:decidePolicyForNavigationAction:decisionHandler:` (call `decisionHandler(WKNavigationActionPolicyAllow/Cancel)`) |
   | `stopLoading`, `goBack`, `goForward`, `reload` | Same methods exist on `WKWebView`                                                                                         |

3. **Construction & Configuration**

   * Replace ad-hoc `UIWebView` init with:

     ```objc
     WKWebViewConfiguration *config = [WKWebViewConfiguration new];
     config.allowsInlineMediaPlayback = YES; // if previously used
     config.preferences.javaScriptEnabled = YES; // mirror prior behavior if needed
     config.websiteDataStore = [WKWebsiteDataStore defaultDataStore];

     WKWebView *webView = [[WKWebView alloc] initWithFrame:CGRectZero configuration:config];
     webView.navigationDelegate = self;
     webView.UIDelegate = self;
     webView.translatesAutoresizingMaskIntoConstraints = NO;
     ```
   * Add Auto Layout constraints equivalent to the old view’s layout.

4. **JavaScript Bridging**

   * Replace custom JS bridges that used `stringByEvaluatingJavaScriptFromString:` with either:

     * `evaluateJavaScript:completionHandler:` for one-offs; or
     * `WKUserContentController` message handlers for native⇄JS communication:

       ```objc
       config.userContentController = [WKUserContentController new];
       [config.userContentController addScriptMessageHandler:self name:@"nativeBridge"];
       // JS side: window.webkit.messageHandlers.nativeBridge.postMessage(payload);
       ```
   * Implement `-userContentController:didReceiveScriptMessage:` in the receiver.

5. **UI Delegate (alerts, prompts, new windows)**
   Implement common handlers previously handled via `UIWebView` or JS:

   ```objc
   - (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler;

   - (void)webView:(WKWebView *)webView runJavaScriptConfirmPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(BOOL result))completionHandler;

   - (void)webView:(WKWebView *)webView runJavaScriptTextInputPanelWithPrompt:(NSString *)prompt defaultText:(NSString *)defaultText initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(NSString * _Nullable result))completionHandler;
   ```

6. **Navigation Policy Parity**

   * Convert any logic in `shouldStartLoadWithRequest` to `decidePolicyForNavigationAction`.
   * If you previously inspected URLs or navigation types, port that logic using `navigationAction.request` and `navigationAction.navigationType`.

7. **Cookies, Storage, and Cache**

   * If the app relied on `NSHTTPCookieStorage` implicitly: mirror cookies to `WKHTTPCookieStore` before first load:

     ```objc
     WKHTTPCookieStore *cookieStore = webView.configuration.websiteDataStore.httpCookieStore;
     // Set existing cookies as needed; call completion before issuing first load
     ```
   * For clearing data, use `WKWebsiteDataStore` APIs instead of `UIWebView` hacks.

8. **Storyboards / XIBs**

   * Replace `UIWebView` with `WKWebView`. If Interface Builder usage is complex, create the `WKWebView` programmatically in the view controller and connect an `IBOutlet` container view.
   * Document any required manual storyboard changes in the migration notes.

9. **Third-Party & Dead Code**

   * For any Pod/framework that references `UIWebView`, prefer updating to a version that removed it. If not possible, replace the dependency or fork/patch to eliminate references (even string literals can trigger rejection).

10. **Comments & TODOs**

    * Where exact parity is impossible (e.g., `scalesPageToFit`), add a short `// MIGRATION:` comment explaining the modern behavior and any follow-ups.

---

## 2) Example Rewrite (typical controller)

**Before (UIWebView)**

```objc
@interface WebVC () <UIWebViewDelegate>
@property (nonatomic, weak) IBOutlet UIWebView *webView;
@end

@implementation WebVC
- (void)viewDidLoad {
  [super viewDidLoad];
  self.webView.delegate = self;
  [self.webView loadRequest:[NSURLRequest requestWithURL:self.url]];
}
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)type {
  // custom logic…
  return YES;
}
- (void)webViewDidFinishLoad:(UIWebView *)webView { /* … */ }
@end
```

**After (WKWebView)**

```objc
@import WebKit;

@interface WebVC () <WKNavigationDelegate, WKUIDelegate>
@property (nonatomic, strong) WKWebView *webView;
@end

@implementation WebVC
- (void)viewDidLoad {
  [super viewDidLoad];

  WKWebViewConfiguration *config = [WKWebViewConfiguration new];
  config.preferences.javaScriptEnabled = YES;
  config.websiteDataStore = [WKWebsiteDataStore defaultDataStore];

  self.webView = [[WKWebView alloc] initWithFrame:CGRectZero configuration:config];
  self.webView.navigationDelegate = self;
  self.webView.UIDelegate = self;
  self.webView.translatesAutoresizingMaskIntoConstraints = NO;
  [self.view addSubview:self.webView];

  [NSLayoutConstraint activateConstraints:@[
    [self.webView.topAnchor constraintEqualToAnchor:self.view.topAnchor],
    [self.webView.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor],
    [self.webView.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor],
    [self.webView.bottomAnchor constraintEqualToAnchor:self.view.bottomAnchor],
  ]];

  // If cookies/session are required, set via httpCookieStore here before first load.

  NSURLRequest *req = [NSURLRequest requestWithURL:self.url];
  [self.webView loadRequest:req];
}

// Navigation policy (replaces shouldStartLoadWithRequest)
- (void)webView:(WKWebView *)webView
decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction
decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
  NSURL *url = navigationAction.request.URL;
  // custom logic…
  decisionHandler(WKNavigationActionPolicyAllow);
}

- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)nav { /* … */ }
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)nav { /* … */ }
- (void)webView:(WKWebView *)webView didFailNavigation:(WKNavigation *)nav withError:(NSError *)error { /* … */ }

@end
```

---

## 3) Editing Rules & Safety

* **Make atomic changes** per file and output **unified diffs** (`--- old +++ new`) so reviewers can inspect each patch.
* **Never** leave code uncompilable; update imports (`@import WebKit;`), property attributes, and protocols consistently.
* Add minimal, clear comments where behavior changes (e.g., viewport/scaling).
* Do **not** invent new business logic; preserve existing flows.

---

## 4) Output Format (strict)

1. **Summary**: bullet list of files changed, added, or flagged (including Pods or third-party offenders).
2. **Per-file unified diff** blocks of your edits.
3. **Migration Notes**: any non-parity items (e.g., `scalesPageToFit`) and required storyboard steps.
4. **Follow-up Checklist** (see below).

---

## 5) Post-Migration Validation Checklist

* [ ] Build imports: `@import WebKit;` present where needed.
* [ ] All delegates updated to `WKNavigationDelegate` / `WKUIDelegate`.
* [ ] Replaced `stringByEvaluatingJavaScriptFromString:` with `evaluateJavaScript:completionHandler:`.
* [ ] Navigation decision logic faithfully ported.
* [ ] JS alerts/confirm/prompt handled via `WKUIDelegate`.
* [ ] Cookies/session set via `WKHTTPCookieStore` (if app depended on them).
* [ ] Any scaling behavior documented and verified with proper viewport meta.
* [ ] Storyboards/XIBs updated or programmatic view used.
* [ ] No remaining references to `"UIWebView"` anywhere in the repo (including Pods).
* [ ] App launches and web content functions as before.

---

## 6) Additional Notes

* If minimum iOS is < 11, wrap cookie-store usage with availability checks and fall back to server-driven sessions.
* For file uploads / target=_blank, implement `WKUIDelegate`’s window-creation and file-picker flows if the app uses them.
* For progress UI, consider `estimatedProgress` KVO on `WKWebView`.

---

**Now perform the migration as specified and return the Summary, Diffs, and Migration Notes.**
