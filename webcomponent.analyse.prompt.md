---
mode: agent
---
# In-Project Web Component Analysis (TS/JS) — **Complete Technical Documentation v2**

You are a **senior frontend engineer**. Perform **static analysis only** (no execution). Produce a **single Markdown report** that strictly follows the **Output Contract** below.

## Inputs

- **Entry file path:** `<ENTRY_FILE>` (treat current chat context as repo root unless stated otherwise).
- Optional config files if present and reachable: `tsconfig.json`, `jsconfig.json`, `package.json`, framework configs.
- Analyze **all code reachable** via imports from `<ENTRY_FILE>` (respect rules below).

## Scope & Resolution Rules

- **Working directory:** repo root (current chat context).
- **Module resolution:** Node/ESM semantics. Prefer local files over dependencies. Resolve:

  - Relative imports `./` `../`
  - Path aliases from `tsconfig.json/jsconfig.json` (`compilerOptions.paths`, `baseUrl`)

- **Include:**

  - Files reachable via imports from the entry (TS/JS/TSX/JSX, CSS-in-JS, template files in same tree).
  - Local utilities/helpers.
  - Framework-specific modules (Lit, Stencil, etc.) if referenced.

- **Exclude:**

  - `node_modules` (unless directly imported by a project file you’re analyzing).
  - Build outputs: `dist`, `build`, `.next`, `out`.
  - Tests/stories/examples unless **directly** imported at runtime.
  - Type-only imports that do not affect runtime.

- **Monorepos:** If `<ENTRY_FILE>` is inside a package, treat that package as the root for local resolution but still honor cross-package imports when explicitly referenced.

## Component Types (Adapt Terminology, Keep Structure)

- **Native Custom Elements:** focus on `constructor`, `connectedCallback`, `disconnectedCallback`, `attributeChangedCallback`, `adoptedCallback`, `static get observedAttributes()`, `customElements.define`.
- **Framework Wrappers (Lit, Stencil, etc.):** map to equivalent lifecycle, reactivity, templating, & styling constructs.
- **Hybrid/Bridged:** document both native and framework APIs.

## Evidence & Citations (Static Only)

When asserting facts, **cite code locations** as `path/to/file.ts:lineStart–lineEnd` and (optionally) include **≤5 line** snippets where clarification helps. Do not execute code.

---

## Output Contract (Markdown)

Produce a comprehensive report with the **exact numbered sections** below. Use **exact identifier names** from source. Mark inferred items with **“(inferred)”**.

### 1. Executive Summary

- **Purpose:** component’s primary function and UI role.
- **Type:** Native / Lit / Stencil / React / Other.
- **Complexity:** Simple / Medium / Complex (justify briefly).
- **Key Dependencies:** major external libraries/frameworks used (with file cites).

### 2. Quick Reference

| Aspect         | Details               |
| -------------- | --------------------- |
| Tag Name       | `<component-name>`    |
| Key Attributes | 3–5 most important    |
| Main Events    | 2–3 primary events    |
| CSS Variables  | Count or “None”       |
| Shadow DOM     | Yes/No, Open/Closed   |
| Framework      | Native/Lit/React/etc. |

> If multiple custom elements are defined in the entry graph, create one **Quick Reference** row **per element** and note the count in the Executive Summary.

### 3. Public API (Consumer-Facing)

#### 3.1 Attributes / Properties

| Name | Kind (Attr/Prop) | Type | Default | Required | Reflects | Observed | Description | Evidence |
| ---- | ---------------- | ---- | ------- | -------- | -------- | -------- | ----------- | -------- |

- Distinguish HTML attributes vs JS properties. Note sync patterns (e.g., property-to-attribute reflection, Lit `@property({ reflect: true })`).

#### 3.2 Slots

| Name | Required | Purpose | Fallback Content | Evidence |
| ---- | -------- | ------- | ---------------- | -------- |

#### 3.3 CSS Custom Properties

| Property | Expected Values | Default | Effect | Evidence |
| -------- | --------------- | ------- | ------ | -------- |

#### 3.4 CSS Shadow Parts

| Part Name | Element | Purpose | Evidence |
| --------- | ------- | ------- | -------- |

#### 3.5 Methods

| Method | Signature | Visibility (public/protected/private) | Side Effects | Returns | Throws | Evidence |
| ------ | --------- | ------------------------------------- | ------------ | ------- | ------ | -------- |

#### 3.6 Events

| Event Name | `detail` Type | When Fired | Bubbles | Composed | Cancelable | Evidence |
| ---------- | ------------- | ---------- | ------- | -------- | ---------- | -------- |

#### 3.7 Outputs Summary

- **Return Values:** methods consumers rely on.
- **Event Data:** critical `detail` shapes (include TS types where available).
- **State Exposure:** public readable state.

### 4. Usage Examples

#### 4.1 Basic Usage

```html
<!-- Basic instantiation -->
<my-component attribute="value" required-prop="data">
  <span slot="header">Title</span>
  Default content here
</my-component>
```

#### 4.2 JavaScript Integration

```ts
const el = document.createElement("my-component");
el.setAttribute("data", "value");
el.addEventListener("change", (e: CustomEvent<{ value: string }>) => {
  console.log("Changed:", e.detail.value);
});
document.body.appendChild(el);

// Methods
(el as any).validate?.();
(el as HTMLElement).focus();
```

#### 4.3 Styling Examples

```css
my-component {
  --component-color: blue;
  --component-size: 2rem;
}

my-component::part(container) {
  border: 1px solid #ccc;
  border-radius: 4px;
}
my-component::part(button) {
  background: var(--primary-color);
}
```

#### 4.4 Framework Integration

```tsx
// React example
<MyComponent
  data={state.data}
  onChange={(e: CustomEvent<{ value: string }>) =>
    setState({ data: e.detail.value })
  }
>
  <span slot="header">{title}</span>
</MyComponent>
```

### 5. Internal Design & Architecture

#### 5.1 File Structure & Dependencies

Provide a high-level tree from the entry file and list external deps.

```
entry-file.ts
├─ ./utils/helper.ts
├─ ./styles/theme.css
├─ ../shared/base-component.ts
└─ external: lit, rxjs
```

#### 5.2 State Management

- **State Model:** structure and mutation sources.
- **State Flow:** how changes propagate to rendering/events.
- **Persistence:** any state persisting across lifecycles.

#### 5.3 Lifecycle & Rendering

- **Construction**
- **Connection / Mount**
- **Updates / Reactivity** (e.g., `attributeChangedCallback`, Lit reactive fields)
- **Disconnection / Cleanup**
- **Rendering Strategy:** templates/VDOM/manual DOM ops.

#### 5.4 Data Flow

```
Inputs (attributes/props) → Processing → Internal State → Render → Events/Outputs
```

Note async ops, timers, observers, and side effects.

#### 5.5 Side Effects & External Interactions

- Shadow DOM boundary crossings
- Network requests
- Timers/Intervals
- Observers (Resize/Mutation/Intersection)
- Global listeners (window/document)

#### 5.6 Error Handling

- Validation strategies
- Error reporting to consumers (events, thrown errors)

### 6. TypeScript/JavaScript Specifics

#### 6.1 Type Definitions

- Public interfaces & consumer types (exported).
- Internal types/generics; runtime vs compile-time checks.

#### 6.2 JS/TS Patterns

- Class vs function composition
- Mixins, decorators, base classes
- Runtime guards (zod, `instanceof`, custom validators)

### 7. Performance & Optimization

#### 7.1 Characteristics

- Render cost & bottlenecks
- Memory patterns & cleanup
- Bundle impact & tree-shaking
- Initialization cost

#### 7.2 Strategies

- Memoization, caching
- Lazy loading/splitting
- Debounce/throttle
- Observer batching

### 8. Accessibility & Standards

#### 8.1 A11y Features

- ARIA roles/attributes
- Keyboard navigation map (list keys & focus order)
- Screen reader considerations
- Color/contrast tokens

#### 8.2 Web Standards Compliance

- Proper `customElements.define` usage (or framework equivalent)
- Shadow DOM encapsulation & style scoping
- Semantics and form association (e.g., `ElementInternals`, `formAssociated`)

### 9. Browser Compatibility & Limitations

#### 9.1 Support Matrix

- Minimum versions / polyfills / feature detection

#### 9.2 Known Limitations

- API restrictions, browser quirks, styling constraints

#### 9.3 Common Pitfalls

- Typical misuse, integration issues, debugging tips

### 10. Development & Testing Guidance

#### 10.1 Key Test Scenarios

- Unit/integration/a11y/perf/cross-browser
- Event typing & contract tests
- Attribute/property reflection tests

#### 10.2 Dev Workflow

- Local development tips
- Build & bundling notes
- Debugging recommendations (e.g., inspect shadow roots, event logging)

---

## Analysis Guidelines

- **Accuracy:** Use exact identifiers. Quote **short** evidence snippets (≤5 lines) with file/line cites when clarifying.
- **Inference:** Mark inferred behavior with **“(inferred)”** and explain reasoning.
- **Gaps:** If data is missing, follow the **Missing Information Protocol**.
- **Multi-Component Files:** Document each component separately, then provide a brief comparative note in the Executive Summary.
- **Security/Privacy:** Flag risky patterns (e.g., `innerHTML` with untrusted input, global event leakage).

## Missing Information Protocol

If any section cannot be completed:

1. **State what’s missing** (files, exports, types).
2. **Where it should be** (expected path/import).
3. **Partial analysis** using available info (mark **inferred**).
4. **Next steps**: list specific files/lines that would unblock completion.

## Reviewer Checklist (for you to self-check before submitting)

- [ ] All sections 1–10 present.
- [ ] Distinguish attributes vs properties; reflection documented.
- [ ] Events include `detail` typing and bubble/composed/cancelable flags.
- [ ] Slots, CSS custom properties, and shadow parts enumerated (or “None”).
- [ ] Lifecycle methods mapped and cited with file:lines.
- [ ] A11y: roles, keyboard map, focus handling described.
- [ ] Perf and cleanup (timers/observers/listeners) accounted for.
- [ ] Known limitations/pitfalls populated.
- [ ] Missing Information Protocol followed where applicable.

---

**Deliverable:** Return **only** the Markdown report per the **Output Contract** above. No extraneous commentary.
