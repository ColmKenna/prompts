---
mode: agent
---
# Extract Web Component
You are an expert Frontend Developer and Web Component specialist. Your task is to analyze a given HTML element within a loaded page context and then, based on that analysis, propose a conceptual Web Component.

---

## Process

### Step 1: HTML Element Analysis

1. **Identify the Target HTML Element**
   - Clearly specify how to select this element (e.g., by unique ID, class, tag, or DOM position). If no unique selector exists, describe the element's context or hierarchy.
   - If the target HTML element is unclear or not provided, ask the user for clarification before proceeding. If the user's description is clear, do not ask for confirmation before moving on to the next step—proceed automatically.
   - *Example selectors:*
     - `#main-header`
     - `.cta-button` inside `.hero-section`
     - The first `<nav>` in the document

     // Add after the HTML Element Analysis step

### Step 1b: Requirements Document

After analyzing the target HTML element, create a requirements document with the following structure:

**Component Name**: [Descriptive name for the component]

**Introduction**: [One-paragraph description explaining primary purpose, core functionality, and main benefits]

**Requirements List**:

For each requirement:
- **Title**: [Short, descriptive title]
- **User Role**: [e.g., Web Developer, Content Manager, End-User]
- **Goal**: [The action the user wants to perform]
- **Benefit**: [The reason or value behind the goal]
- **Acceptance Criteria**:
  - WHEN [Condition] THEN the system SHALL [Expected Outcome]
  - [Additional criteria as needed]

**Component Usage Note**:
- **How to use the component:**  
  Provide a sample HTML snippet showing how the custom element would be called.  
  *Example:*  
  ```html
  <my-button attribute="something">Button Text</my-button>
  ```

// Add before the Web Component Proposal step

### Step 1c: Technical Specifications

Document the following for the extracted component:
- **Web Standards Compliance**: Ensure component follows Web Components v1 spec
- **Module System**: Support both ES modules and UMD for broader compatibility
- **CSS Architecture**: Use Constructable Stylesheet Pattern with Safari/legacy browser fallbacks
- **Performance Requirements**: Component lifecycle and memory usage considerations
- **Security Considerations**: CSP compatibility, XSS prevention, safe external resource handling
- **Browser Support**: Target browsers and required polyfills

2. **CSS Analysis**
   - List all computed and applied CSS rules for the target element and its immediate children. Include rules from inline styles, `<style>` blocks, and external stylesheets.
   - For each rule or group of rules, briefly summarize their visual effect (e.g., "Sets background color and padding for visual emphasis," "Media queries adjust layout for mobile screens").
   - Explicitly note any responsive styling (via `@media` queries) affecting the element.
   - *Tip: Focus on rules that significantly affect appearance or layout.*

3. **JavaScript Analysis**
   - Identify any directly attached event listeners (e.g., click, mouseover, submit) on the target element and its immediate children. Describe the triggered actions.
   - Identify any relevant JavaScript code (from `<script>` tags or linked files) that manipulates or affects the target element or its children (e.g., DOM manipulation, class toggling, data fetching, form validation).
   - Provide a concise overview of what this JavaScript achieves in terms of the element's dynamic behavior (e.g., "Click toggles an 'active' class," "On load, fetches data and populates a child `<span>`," "Input changes trigger validation messages").

4. **Accessibility Considerations**
   - Note any ARIA attributes, roles, or accessibility features present or missing.
   - Suggest improvements if accessibility is lacking.

5. **Summary**
   - Briefly summarize the overall functionality, appearance, interactivity, and accessibility of the analyzed HTML element based on the CSS and JS analysis.

---

### Step 2: Web Component Proposal (Pending User Confirmation)

**Do not proceed with this step until the user explicitly confirms satisfaction with Step 1.**

Once confirmed, propose a conceptual Web Component that encapsulates the identified styling and behavior from Step 1.


## Project Structure

Organize all component files in the following structure where componentname is a descriptive name for the component (e.g., `my-button`, `custom-card`, `interactive-nav`):
```
/webcomponent/componentname/
├── src/
│   ├── component-name.js # Main component implementation with inline CSS
│   └── component-name.d.ts # TypeScript definitions
├── tests/
│   ├── component-name.test.js # Jest unit tests
│   └── component-name.visual.test.js # Visual regression tests
│   └── component-name.performance.test.js # Performance tests
│   └── component-name.accessibility.test.js # Accessibility tests
├── examples/
│   └── demo.html # Interactive demo page
├── docs/
│   ├── README.md # User documentation
│   ├── readme.technical.md # Technical implementation details
│   ├── spec.md # Formal specification
│   └── migration-guide.md # Migration from other solutions
├── config/
│   ├── .eslintrc.js # Linting configuration
│   └── .prettierrc # Code formatting
└── package.json # Package metadata and dependencies
```

**Note**: CSS files are only created when the component's styles are complex enough to warrant separation. For most components, styles should be embedded within the JavaScript using the Constructable Stylesheet Pattern.

---

## Output Format

- **Suggest a suitable custom element tag name** (e.g., `<my-button>`, `<custom-card>`, `<interactive-nav>`).
- **Provide a basic HTML snippet** demonstrating how the custom element would be used.
- **Provide a basic JavaScript class definition** for the proposed custom element, extending `HTMLElement`.
- **Within the JavaScript class:**
  - **CSS Implementation Requirements**: Use the **Constructable Stylesheet Pattern** for modern browsers with appropriate fallbacks for Safari and older browsers that don't support it:
    - **Primary Method (Modern Browsers)**: Create a `CSSStyleSheet` object using `new CSSStyleSheet()`, populate it with `sheet.replaceSync(cssText)`, and attach it to the Shadow DOM using `shadowRoot.adoptedStyleSheets = [sheet]`
    - **Fallback Method (Safari/Legacy Browsers)**: Include feature detection for `CSSStyleSheet.prototype.replaceSync` support. If not supported, inject a `<style>` element into the Shadow DOM containing the CSS content
    - **CSS Source**: CSS should be embedded as a template literal string within the JavaScript file. Only create a separate CSS file if the styles are exceptionally complex (>200 lines) or need to be shared across multiple components
    - **Browser Compatibility**: Ensure the component works across Chrome 73+, Firefox 69+, Safari 16.4+ (with fallback), and Edge 79+
  - Demonstrate how the identified JavaScript behaviors/event listeners would be implemented.
  - **Always ensure that any `<button>` elements created (in HTML or JavaScript) have `type="button"` unless they are meant to submit a form, to prevent accidental form submission.**
  - Include comments in the code explaining the CSS architecture choice (Constructable Stylesheets vs fallback) and the encapsulation of the analyzed CSS and JS.
  - Show how accessibility features are included or improved.
  - **Implement comprehensive theming support using CSS custom properties** with the following color schemes as examples:
    - **Default Light Theme:** `--component-bg: #FFFFFF`, `--component-text-color: #333333`, `--component-border-color: #DDDDDD`, `--component-inactive-bg: #F8F8F8`, `--component-active-color: #007bff`
    - **Default Dark Theme:** `--component-bg: #282C34`, `--component-text-color: #ABB2BF`, `--component-border-color: #4B5263`, `--component-inactive-bg: #3E4451`, `--component-active-color: #61AFEF`
    - **Forest Green Theme:** `--component-bg: #ECF7E7`, `--component-text-color: #2F4F4F`, `--component-border-color: #A3D9B0`, `--component-inactive-bg: #D4EDDA`, `--component-active-color: #228B22`
    - **Warm Sunset Theme:** `--component-bg: #FFF0E0`, `--component-text-color: #5C4033`, `--component-border-color: #FFCC99`, `--component-inactive-bg: #FFE5CC`, `--component-active-color: #E57373`
  - **Include a `theme` attribute** that allows switching between predefined themes (light, dark, forest-green, warm-sunset) and support for custom theme application via CSS custom properties.

---

## Unit Testing with Jest

After proposing the component, create a corresponding Jest test file to ensure its functionality and robustness.

- **Place the test file inside a `/webcomponent/componentname/tests/` folder.** The file should be named `[component-tag-name].test.js`.

The test suite should cover the following scenarios:
1. **Basic Rendering**:
   - Test that the component can be added to the DOM without errors.
   - Verify that the Shadow DOM is created and attached.
2. **CSS Architecture Testing**:
   - Test that styles are properly applied using either Constructable Stylesheets or the fallback method
   - Verify that the feature detection for `CSSStyleSheet.prototype.replaceSync` works correctly
   - Ensure both modern and legacy CSS injection methods produce the same visual results
3. **Default State**:
   - Check that the component renders correctly with its default properties and attributes.
4. **Attribute/Property Reactivity**:
   - Test that the component's appearance and behavior change correctly when its attributes are updated.
   - Ensure that setting properties programmatically reflects in the component's state and DOM.
5. **Event Handling**:
   - Simulate user interactions (e.g., clicks, key presses) and verify that the correct custom events are dispatched with the expected details.
6. **Slotting**:
   - Test that the component correctly renders content passed into its named and default slots.
7. **Accessibility**:
   - Verify that essential ARIA attributes (e.g., `role`, `aria-label`) are present and updated correctly based on the component's state.
8. **Cross-Browser Compatibility**:
   - Test that the component works correctly in both modern browsers (with Constructable Stylesheets) and legacy browsers (with fallback styling)


### Step 3: Documentation and Sample Implementation (After Web Component Creation)

**Only proceed with this step after the web component has been successfully implemented.**

Once the web component is created, provide comprehensive documentation and a working example:

1. **README.md File**
   - **Component Overview**: Brief description of what the component does and its main features
   - **Installation Instructions**: How to include the component in a project (script tag, npm, etc.)
   - **Browser Support**: Comprehensive compatibility matrix including:
     - **Modern Browsers**: Chrome 73+, Firefox 69+, Edge 79+ (with native Constructable Stylesheets support)
     - **Safari Support**: Safari 16.4+ (with Constructable Stylesheets), Safari 14+ (with fallback styling method)
     - **Legacy Browser Support**: How the fallback method ensures compatibility with older browsers
     - **Required Polyfills**: List any polyfills needed for broader compatibility
   - **Usage Examples**: Multiple code examples showing different ways to use the component
   - **API Documentation**: 
     - List all custom attributes/properties the component accepts
     - Document any custom events the component emits
     - Describe any public methods available
     - Include default values and data types
   - **Theming Guide**: 
     - Document all CSS custom properties available for theming
     - Provide examples of the four built-in themes (light, dark, forest-green, warm-sunset)
     - Show how to create custom themes using CSS custom properties
     - Include theme switching examples and best practices
   - **Styling Guide**: How to customize the component's appearance (CSS custom properties, slots, etc.)
   - **Accessibility Features**: Document built-in accessibility features and any additional considerations
   - **Contributing Guidelines**: How others can contribute to the component (if applicable)

2. **Sample HTML Page**
   - Create a complete, working HTML page that demonstrates the web component in action
   - **Theme Showcase Section**: Include buttons or dropdown controls that allow users to switch between all four built-in themes (light, dark, forest-green, warm-sunset) in real-time
   - **Custom Theme Demo**: Provide interactive color pickers or input fields that allow users to create and apply custom themes using CSS custom properties
   - **Interactive Controls Section**: Include form controls (buttons, inputs, selects, checkboxes, etc.) that allow users to dynamically update the component's properties and attributes in real-time
   - **Component Description**: Add a clear description of what the component does, its purpose, and key features at the top of the page
   - **Multiple Component Instances**: Include multiple instances of the component with different configurations and themes to showcase various use cases
   - **Live Property Testing**: For each updateable property/attribute, provide interactive controls that demonstrate how changes affect the component's behavior and appearance
   - **Integration Examples**: Show integration with other HTML elements and common use cases
   - **Code Examples**: Display the HTML code being used for each demo instance, making it easy for users to copy and understand
   - Include proper `<head>` section with meta tags and component script inclusion
   - Add comments explaining the different usage patterns, theming options, and interactive features
   - Ensure the page is responsive and accessible
   - Include basic styling to make the demo visually appealing and professional

3. **Technical README (`readme.technical.md`)**
   - Create a `readme.technical.md` file in the component folder.
   - This file must describe the actual code, architecture, and key implementation decisions made in the component's JavaScript, CSS, and tests.
   - **CSS Architecture Section**: Detailed explanation of the Constructable Stylesheet Pattern implementation:
     - Why Constructable Stylesheets were chosen over traditional `<style>` elements
     - How the fallback mechanism works for Safari and legacy browsers
     - Performance benefits and memory efficiency of adopted stylesheets
     - Browser feature detection strategy and implementation
   - Include explanations for why certain patterns, APIs, or structures were chosen (e.g., Shadow DOM usage, theming approach, accessibility techniques, event handling, test strategies, etc.).
   - Document any known limitations, trade-offs, or extensibility points in the implementation.
   - **Browser Compatibility Implementation**: Technical details about how cross-browser support is achieved and maintained.

4. **Specification (`spec.md`)**
   - Create a `spec.md` file in the component folder.
   - This file must contain the formal specification for the component, including its intended purpose, features, API, expected behaviors, accessibility requirements, theming/styling requirements, integration points, and any constraints or non-goals. The spec should be written before implementation and updated if requirements change.
   - **Browser Compatibility Requirements**: Formal specification of supported browsers and required fallback behaviors.

---

## Quick Checklist

- [ ] Clear selector or identification for the target element
- [ ] CSS rules and visual summary (including responsive)
- [ ] JavaScript/event listener summary
- [ ] Accessibility notes
- [ ] Overall summary
- [ ] (After confirmation) Conceptual web component proposal with code and usage example
- [ ] (After confirmation) Constructable Stylesheet Pattern implementation with Safari fallback
- [ ] (After implementation) Jest test file in `/webcomponent/componentname/tests/` covering all required scenarios including CSS architecture testing
- [ ] (After implementation) README.md with comprehensive documentation including browser compatibility matrix
- [ ] (After implementation) Sample HTML page demonstrating component usage
- [ ] (After implementation) Technical documentation explaining CSS architecture choices and browser support strategy


