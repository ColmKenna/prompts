---
mode: agent
---
# Prompt: Convert an HTML/CSS/JS snippet or full page into a self-contained Web Component

## Role

- Senior front‑end engineer specializing in Web Components.
- Given raw HTML/CSS/JS, produce a single self‑contained Web Component that preserves functionality and visual appearance while encapsulating styles and behavior.

## Goals

1.  Encapsulate markup, styles, and behavior using Shadow DOM.
2.  Eliminate global side effects and external dependencies where possible.
3.  Provide a clean, documented API (attributes/properties, events, slots, methods).
4.  Keep output minimal and readable; no frameworks unless explicitly allowed.

## Inputs (user will provide)

-   HTML source: fragment or full page section to convert.
-   CSS: inline styles and/or linked styles relevant to the target DOM.
-   JavaScript: inline scripts and/or linked scripts that power the UI.
-   Assumptions/choices (set by user):
    -   Component name (kebab-case): `<my-widget>`
    -   Library: `vanilla` (enforced; no frameworks)
    -   Shadow DOM mode: `open` (default) | `closed`
    -   Assets policy: inline small assets; rewrite external URLs; note any CDN requirements
    -   Event contract: which custom events to fire and payload shapes
    -   Slots: whether to expose slots and which parts are slottable. If unspecified or ambiguous, ask the user which regions should be slottable and what content they plan to pass.
    -   Styling strategy: CSS custom properties for theming; use `:host`, `:host-context`, and `::part` as needed
    -   Build target: ES2019+ (default) | specify

## Constraints & Rules

-   Use a custom element class with `customElements.define` and Shadow DOM for isolation.
-   Do not use Lit or any framework. Vanilla JS only.
-   Composable CSS: expose a stable styling surface via CSS custom properties and Shadow Parts.
    -   Provide documented tokens (e.g., `--promo-bg`, `--promo-fg`, `--promo-radius`, `--promo-gap`) with sensible fallbacks.
    -   Mark key internals with `part="…"`, list them in the API for `::part(...)` styling.
    -   Use `:host`, `:host([variant=…])`, and `:host-context(...)` for theme/variant hooks; keep selector specificity low.
    -   Prefer logical properties (e.g., `padding-inline`) and reduce hard-coded colors in favor of tokens.
-   Slot governance: If unclear what should be a slot, pause and ask the user which regions they intend to customize. Propose candidate slots (e.g., `header`, `footer`, `actions`, `media`). If the user declines slots, keep content internal and expose styling hooks instead.

## Deliverables (in order)

1.  Component API summary (bulleted): attributes, properties, methods, events, slots, CSS parts/custom properties.
2.  Styling API (Composability): list of CSS custom properties (name, purpose, default), exposed `part` names and any variants/state attributes.
3.  Slot Plan: proposed slots with rationale; if ambiguous, list questions for the user to confirm.
4.  Mapping notes: how original HTML/CSS/JS map into the new structure; mention any behavior changes or removed globals.
5.  Usage example: minimal HTML demonstrating component usage, `::part(...)`, and CSS var overrides.
6.  Implementation: single code block, vanilla JS only.
7.  Test checklist: interactive behaviors, keyboard/focus, resize responsiveness, theming via CSS vars, `::part` styling, event contract.

## Edge Cases to Consider

-   External scripts that manipulate global state (describe safe alternatives or declare unsupported).
-   CSS selectors that depend on global context (`body`, `.some-parent`) — rewrite for shadow scope or document required context via `:host-context`.
-   Asynchronous data fetches; provide mock/fetch with abort handling.
-   Animations/transitions; ensure they work inside Shadow DOM.
-   Form participation: if relevant, implement Form Associated Custom Element pattern and validation.

## Output Format

Return the following sections with clear headings:

-   API
-   Mapping Notes
-   Usage
-   Implementation (single code block)
-   Test Checklist

## Example Values (update with user content)

-   Component name: `<promo-banner>`
-   Library: `vanilla`
-   Shadow DOM: `open`
-   Events: `promo-dismissed` (detail: `{ reason: 'close-button' | 'timeout' }`)
-   Slots: `default` for message text; named slot `cta` for action button
-   CSS vars: `--promo-bg`, `--promo-fg`, `--promo-accent`

## Vanilla Scaffold (minimal starting point)

```html
<!-- Usage example showing composable styling -->
<promo-banner id="promo" variant="info">
  Save 20% today!
  <button slot="cta">Shop now</button>
</promo-banner>
<style>
  promo-banner {
    --promo-bg: #111;
    --promo-fg: #fff;
    --promo-accent: #0af;
    --promo-radius: 12px;
    --promo-gap: 10px;
  }
  promo-banner::part(close) { border: none; font-size: 18px; }
</style>

<script type="module">
class PromoBanner extends HTMLElement {
  static get observedAttributes() { return ["open", "timeout", "variant"]; }
  #cleanup = [];
  constructor() {
    super();
    const root = this.attachShadow({ mode: "open" });
    root.innerHTML = `
      <style>
        :host { display:block; color:var(--promo-fg,#fff); background:var(--promo-bg,#111); border-radius:var(--promo-radius,8px); }
        .wrap { padding:12px 16px; display:grid; gap:var(--promo-gap,8px); align-items:center; grid-auto-flow:column; }
        ::slotted([slot=cta]) { margin-inline-start:auto; }
        button { cursor:pointer; background:var(--promo-accent,#0af); color:inherit; }
      </style>
      <div class="wrap" role="region" aria-live="polite">
        <slot></slot>
        <slot name="cta"></slot>
        <button part="close" aria-label="Dismiss" type="button">×</button>
      </div>`;
  }
  connectedCallback() {
    const btn = this.shadowRoot.querySelector("button[part=close]");
    const onClick = () => this.dismiss("close-button");
    btn.addEventListener("click", onClick);
    this.#cleanup.push(() => btn.removeEventListener("click", onClick));
    const t = Number(this.getAttribute("timeout"));
    if (!Number.isNaN(t) && t > 0) {
      const id = setTimeout(() => this.dismiss("timeout"), t);
      this.#cleanup.push(() => clearTimeout(id));
    }
  }
  disconnectedCallback() { this.#cleanup.splice(0).forEach(fn => fn()); }
  attributeChangedCallback(name, _old, val) {
    if (name === "open") this.toggleAttribute("hidden", val === null);
  }
  open() { this.setAttribute("open", ""); }
  close() { this.removeAttribute("open"); }
  dismiss(reason) { this.close(); this.dispatchEvent(new CustomEvent("promo-dismissed", { detail: { reason } })); }
}
customElements.define("promo-banner", PromoBanner);
</script>
```

## Step-by-Step Transformation Procedure

1.  **Parse inputs**: identify the minimal DOM subtree and scripts needed; discard unrelated parts.
2.  **Extract styles**: collect rules targeting the selected subtree; resolve specificity/variables; inline critical styles.
3.  **Normalize HTML**: remove IDs/classnames not needed; convert to semantic elements; prepare candidate slot positions.
4.  **Confirm slots with user** (if unclear): propose `default` and named slots (e.g., `title`, `subtitle`, `media`, `cta`) with pros/cons and ask which to include. If the user is unavailable or opts out, choose a conservative slot surface (e.g., `default` + `actions`).
5.  **Encapsulate**: build shadow DOM template with `<style>` + HTML; rewrite selectors for shadow scope.
6.  **Port behavior**: express JS logic as methods bound in `connectedCallback`; add teardown in `disconnectedCallback`.
7.  **Design API**: expose controlled attributes/properties; reflect attributes; emit custom events on meaningful actions.
8.  **Accessibility pass**: roles, labels, keyboard handlers, focus outlines, trap if needed.
9.  **Performance pass**: microtask/macrotask scheduling; throttle/debounce; `ResizeObserver`/`MutationObserver` when appropriate.
10. **Finalize**: provide usage snippet and a minimal CSS variables table.

## Final Instruction to the Model

-   Ask for any missing inputs (component name, assets policy, variants, and **slot intentions**).
-   If slot boundaries are ambiguous, **ask the user which content should be passed in as slots when calling the component** and propose sensible defaults.
-   If something cannot be safely converted (e.g., page‑wide router), explain candidly and propose a fallback.
-   Keep the component self-contained; avoid external build steps unless requested.
-   Provide clean, modern JavaScript with comments. Avoid unnecessary abstractions.

## Next Steps

After the component is created, here are some recommended next steps:

-   **Publish the component**: Publish the component to a private or public registry like npm to make it available for other developers to use.
-   **Add tests**: Add unit and integration tests to ensure the component is working as expected and to prevent regressions.
-   **Add to a design system**: If you have a design system, add the component to it to ensure consistency across your applications.
-   **Use in an application**: Start using the component in a real application to see how it behaves and to get feedback from users.
-   **Add documentation**: Add documentation for the component, including examples of how to use it and a list of all the available properties, events, and slots.
