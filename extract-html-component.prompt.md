---
mode: agent
---
# Project Structure

All extracted component files must be organized in the following structure:

```
/htmlcomponent/componentname/
├── src/
│   ├── component-name.css  # Extracted styles with theme variables
│   ├── component-name.js   # Extracted logic
│   └── component-name.d.ts # Optional TypeScript definitions
├── tests/
│   ├── component-name.test.js # Unit tests
│   └── component-name.visual.test.js # Visual regression tests
├── examples/
│   └── demo.html # Interactive demo page with theme switcher and HTML setup example
├── docs/
│   ├── README.md # User documentation (includes HTML setup examples)
│   ├── readme.technical.md # Technical implementation details
│   ├── spec.md # Formal specification
│   └── migration-guide.md # Migration details
├── config/
│   ├── .eslintrc.js # Linting configuration
│   └── .prettierrc  # Code formatting rules
└── package.json # Metadata and dependencies
```

---

# Technical Specifications

- **Web Standards Compliance**: Extracted component must follow HTML5/CSS3/JS best practices.
- **Module System**: Support ES modules and UMD if possible.
- **Performance Requirements**: Optimize component lifecycle and memory usage.
- **Security Considerations**: Ensure CSP compatibility, XSS prevention, and safe external resource handling.
- **Browser Support**: List target browsers and required polyfills.
- **Accessibility**: Ensure extracted component is accessible (ARIA, keyboard navigation, focus management, etc.).
- **Asset Separation**: Inline CSS/JS must be extracted into .css and .js files.
- **Dependency Detection**: Automatically detect relevant `<link>` and `<script>` assets related to the component.
- **Theming Support**: Implement CSS custom properties for theming (light, dark, forest-green, warm-sunset).
- **Automatic Theme Switcher**: Generate demo page controls to toggle between themes.
- **Security & Performance**: Remove unused assets, ensure CSP and XSS safety, optimize extracted code.

## Documentation & Testing

- **README.md**: Overview, usage, features, and example HTML setup.
- **Technical Docs (readme.technical.md)**: Extraction method, theming system, dependencies, architecture, event system, performance, security, extensibility.
- **Spec.md**: Functional/technical specifications, API, accessibility, performance, security, browser compatibility, integration constraints, non-goals.
- **Migration Guide**: Integration instructions for other projects, breaking changes, compatibility shims, migration tools.
- **Test Files**: Unit, visual regression, performance, accessibility.
- **Configuration Files**: .eslintrc.js, .prettierrc, package.json.

---

# Extract and Theme Existing CSS/JS Component

You are an expert Frontend Developer. Your task is to analyze, extract, theme, and showcase the CSS and JS for a specific HTML component from provided code.

**Do not extract the HTML structure.**  
Instead, provide clear documentation and examples showing how users should set up the required HTML for the component to function.

---

## Process

### Step 1: Requirement Analysis

- **Identify Target Component**
  - Determine which element(s) the CSS and JS affect.
  - If unclear, ask clarifying questions.

- **Detect Associated Assets**
  - Identify CSS and JS related to the component (inline, linked, or external).
  - Include only what directly affects the extracted component.

- **Confirm Scope**
  - Define extraction boundaries.
  - Identify any external libraries or fonts.

- **Clarify:**
  - Which element should be the root of the extracted component?
  - Should external dependencies (e.g., libraries) be bundled or referenced separately?
  - Do you want multiple variations of the component extracted if found?

---

### Step 2: Extraction and Theming

- Extract and refactor CSS into `component-name.css`:
  - Replace color values with CSS variables:
    - `--component-bg`
    - `--component-text-color`
    - `--component-border-color`
    - `--component-inactive-bg`
    - `--component-active-color`
- Extract JS into `component-name.js`, isolating logic specific to this component.
- Provide four built-in themes:
  - Light
  - Dark
  - Forest Green
  - Warm Sunset

---

### Step 3: Documentation and Packaging

- **README.md**: Overview, usage, features, and example HTML setup.
- **Technical Docs (readme.technical.md)**: Extraction method, theming system, dependencies, architecture, event system, performance, security, extensibility.
- **Spec.md**: Functional/technical specifications, API, accessibility, performance, security, browser compatibility, integration constraints, non-goals.
- **Migration Guide**: Integration instructions for other projects, breaking changes, compatibility shims, migration tools.
- **Test Files**: Unit, visual regression, performance, accessibility.
- **Configuration Files**: .eslintrc.js, .prettierrc, package.json.

---

### Step 4: Demo with Automatic Theme Switcher

- `demo.html` must include:
  - Example HTML setup for the component.
  - The extracted CSS and JS in isolation.
  - A theme switcher (dropdown or buttons) to toggle between built-in themes.
  - Example of applying a custom theme via CSS variable overrides.
  - Interactive property controls for real-time updates (optional).

- Ensure demo is:
  - Accessible
  - Responsive
  - Clearly documents usage, theming options, and required HTML structure

---

## Automatic Detection of Dependencies

- Parse provided code for:
  - `<link>` CSS references affecting the component.
  - `<script>` JS references tied to the component.
- Present detected dependencies for confirmation before extraction is finalized.

---

## Output Format

- Suggested component name
- Extracted CSS/JS in `/src`
- Demo page with built-in theme switcher and example HTML setup
- Documentation with theming guide, usage examples, and HTML setup instructions

---

## Checklist

- [ ] Confirm target component
- [ ] Extract and theme CSS
- [ ] Extract associated JS
- [ ] Document required HTML structure and provide examples
- [ ] Implement theme switcher in demo
- [ ] Package in required structure
- [ ] Provide full documentation (README.md, readme.technical.md, spec.md, migration-guide.md) with HTML setup examples
- [ ] Provide comprehensive tests (unit, visual, performance, accessibility)
- [ ] Provide configuration files (.eslintrc.js, .prettierrc, package.json)
- [ ] Document performance, security, accessibility, and extensibility