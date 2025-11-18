---
mode: agent
---
# Create Web Component from Description
You are an expert Frontend Developer and Web Component specialist. Your task is to design and implement a modern, standards-compliant Web Component based solely on a user-provided description.

---

## Project Structure

All component files must be organized in the following structure:
```
/webcomponent/componentname/
├── src/
│   ├── component-name.js # Main component implementation with embedded CSS
│   └── component-name.d.ts # TypeScript definitions
├── tests/
│   ├── component-name.test.js # Jest unit tests
│   └── component-name.visual.test.js # Visual regression tests
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

**Note**: CSS files are only created when styles are exceptionally complex (>200 lines) or need to be shared across multiple components. The default approach is to embed styles within the JavaScript using the Constructable Stylesheet Pattern.

---

## Process

+ Add a Project Pre-check before creating files:
  - BEFORE creating package.json or any configuration files, the generator SHALL inspect the current project (starting from the current working directory and searching up to the workspace root) for existing configuration and metadata files.
  - Files to detect: package.json, .eslintrc.js, .prettierrc, tsconfig.json, jest.config.js, .babelrc, .npmrc, and any existing src/ or config/ folders that look related.
  - If an existing file is found:
    - In interactive mode: present a concise summary of differences and ask whether to (M)erge, (O)verwrite, (S)kip, or (B)ackup-and-overwrite. Default to Merge or Skip.
    - In non-interactive mode: do NOT overwrite existing files. Prefer to Merge where safe (e.g., extending package.json scripts/dependencies), otherwise create a new file with a `.generated` suffix and log a recommendation for manual merge.
    - Always create a timestamped backup (e.g., filename.bak.TIMESTAMP) before overwriting when the user confirms overwrite.
  - If no existing config is present, proceed to create the standard project files as described below.
  - The generator SHALL prefer to extend/augment existing configuration rather than replace it. For example:
    - package.json: merge scripts, devDependencies, and add `files`/exports fields without removing unrelated entries.
    - ESLint/Prettier/TS/Jest configs: add recommended rules/presets through `extends` or merge objects where safe.

### Step 1: Requirements Analysis & Component Proposal

1. **Clarify the Component Description**
   - Carefully read the user's description of the desired component.
   - If any aspect is unclear or ambiguous, ask the user for clarification before proceeding. **Ask all clarification questions in a single prompt, covering all uncertainties at once.**
   - If the user's description is clear, do not ask for confirmation before moving on to the next step—proceed automatically.
   - Identify the main purpose, features, and expected behaviors of the component.
   - List any properties/attributes, events, or slots mentioned or implied in the description.

2. **Requirements Document**
   Create a comprehensive requirements document with the following structure:

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

3. **Technical Specifications**
   - **Web Standards Compliance**: Ensure component follows Web Components v1 spec
   - **Module System**: Support both ES modules and UMD for broader compatibility
   - **Performance Requirements**: Component lifecycle and memory usage considerations
   - **Security Considerations**: CSP compatibility, XSS prevention, safe external resource handling
   - **Browser Support**: Target browsers and required polyfills

---

## Step 2: Implementation Requirements

### Core Component Implementation

- **Suggest a suitable custom element tag name** (e.g., `<my-button>`, `<custom-card>`, `<interactive-nav>`).
- **Provide a basic HTML snippet** demonstrating how the custom element would be used.
- **Provide a modern JavaScript class definition** for the proposed custom element, extending `HTMLElement`.

### JavaScript Implementation Standards

**Within the JavaScript class:**
- **CSS Architecture**: Use the **Constructable Stylesheet Pattern** for modern browsers with appropriate fallbacks for Safari and older browsers:
  - **Primary Method (Modern Browsers)**: Create a `CSSStyleSheet` object using `new CSSStyleSheet()`, populate it with `sheet.replaceSync(cssText)`, and attach it to the Shadow DOM using `shadowRoot.adoptedStyleSheets = [sheet]`
  - **Fallback Method (Safari/Legacy Browsers)**: Include feature detection for `CSSStyleSheet.prototype.replaceSync` support. If not supported, inject a `<style>` element into the Shadow DOM containing the CSS content
  - **CSS Source**: Embed CSS as template literal strings within the JavaScript file. Only create separate CSS files for exceptionally complex styles (>200 lines) or shared stylesheets
  - **Browser Compatibility**: Ensure the component works across Chrome 73+, Firefox 69+, Safari 16.4+ (with fallback), and Edge 79+

- **Event Handling**: Implement described JavaScript behaviors and event listeners with proper cleanup.
- **Form Controls**: Always ensure that any `<button>` elements have `type="button"` unless they are meant to submit a form.
- **Code Documentation**: Include comprehensive comments explaining CSS and JS encapsulation decisions.
- **Performance Optimization**:
  - Implement lazy loading with Intersection Observer for off-screen components
  - Ensure bundle size remains reasonable (< 50KB gzipped)
  - Support tree shaking for unused code elimination
- **Developer Experience**:
  - Support Hot Module Replacement for development workflows
  - Include source maps for debugging
  - Provide clear error messages and warnings

### Accessibility & Modern Standards

- **WCAG 2.1 AA Compliance**: Implement comprehensive accessibility features
- **Keyboard Navigation**: Full keyboard support with proper focus management
- **Screen Reader Support**: Proper ARIA attributes and semantic HTML
- **High Contrast Mode**: Support for Windows High Contrast and `prefers-contrast`
- **Reduced Motion**: Respect `prefers-reduced-motion` for animations
- **Color Contrast**: Ensure all themes meet WCAG contrast requirements

### Advanced Theming System

- The generator SHALL NOT create or embed built-in theme palettes inside the component package.
- All theme definitions MUST live outside the component. Consumers provide design tokens (CSS custom properties) on the host page (e.g., `:root`, `body`, or a wrapper element) and may change them without modifying component code.
- The component MUST consume generically named CSS custom properties with explicit fallback values so it renders predictably even when the host has not yet defined tokens. Preferred patterns include:
  - `background: var(--component-surface, #ffffff)`
  - `color: var(--component-text, #333333)`
  - `border-color: var(--component-border, #dddddd)`
  - `box-shadow: var(--component-elevation, 0 1px 2px rgba(0, 0, 0, 0.12))`
  - `accent-color: var(--component-accent, #007bff)`

Theming Features:
- Theme inheritance: The component reads host-defined CSS variables and relies on its fallbacks only when no external value is supplied. Do not hard-code palettes inside the component.
- External theme control: Provide examples where a page-level class (e.g., `.theme-dark`) sets the variables; the component simply reacts to those changes.
- Theme attribute (optional): A lightweight `theme` attribute MAY be supported solely as a hook to toggle an attribute/class that external styles listen to. The component MUST NOT translate that attribute into hard-coded colors.
- Runtime switching: Theme changes MUST be achieved by updating CSS custom properties on the host (or via class/attribute changes on a wrapper) so themes update dynamically without re-creating the component.
- Custom themes: Consuming pages define their own themes by setting CSS variables; document recommended variable names, meanings, and fallback defaults.
- Documentation: Provide a concise table of CSS variables the component respects and include sample host-level CSS showing how to define or swap themes externally.

Implementation guidance (short):
- In component CSS templates use `var(--token-name, fallback)` everywhere a themeable value appears and keep token names generic (e.g., `--component-surface`, `--component-text-strong`).
- Avoid shipping theme files; provide example CSS snippets in the docs demonstrating how to define themes at the application level (e.g., `:root`, `.app-dark { --component-surface: #121212; }`).
- If supporting a `theme` attribute, limit its responsibility to exposing a selector for external styles (and the demo) to latch onto; do not embed palette data or switch statement logic in the component source.

### Security & Compatibility

- **Content Security Policy**: Ensure compatibility with strict CSP policies
- **XSS Prevention**: Sanitize dynamic content and prevent script injection
- **Safe External Resources**: Implement secure handling of external assets
- **Cross-Origin Considerations**: Handle CORS and cross-origin resource loading

---

## Step 3: Comprehensive Testing Strategy

### Unit Testing with Jest

Create comprehensive test suites to ensure component functionality and robustness:

**Core Test Files:**
- **`[component-tag-name].test.js`**: Main unit tests
- **`[component-tag-name].visual.test.js`**: Visual regression tests
- **`[component-tag-name].performance.test.js`**: Performance and memory tests
- **`[component-tag-name].accessibility.test.js`**: Accessibility compliance tests

**Test Coverage Requirements:**

1. **Basic Rendering & Lifecycle**:
   - Component can be added to DOM without errors
   - Shadow DOM is created and attached correctly
   - Component properly cleans up on disconnection
   - Memory leaks are prevented during lifecycle

2. **State Management**:
   - Component renders correctly with default properties and attributes
   - Attribute/property reactivity works as expected
   - State changes trigger appropriate re-renders
   - Invalid states are handled gracefully

3. **User Interactions**:
   - Simulate user interactions (clicks, key presses, touch events)
   - Verify correct custom events are dispatched with expected details
   - Test keyboard navigation and focus management
   - Validate touch/mobile interactions

4. **Content & Slotting**:
   - Named and default slots render content correctly
   - Dynamic slot content updates work properly
   - Slot fallback content displays when appropriate

5. **Accessibility Testing**:
   - Essential ARIA attributes are present and correct
   - Keyboard navigation follows expected patterns
   - Screen reader announcements are appropriate
   - Color contrast meets WCAG requirements
   - Focus management works correctly

6. **Theming & Styling**:
   - All built-in themes apply correctly
   - Custom theme properties are respected
   - Theme switching works without component re-initialization
   - CSS custom properties cascade properly
   - Constructable Stylesheet Pattern works correctly
   - Fallback styling method functions in unsupported browsers

7. **Performance Testing**:
   - Component initialization time is within acceptable limits
   - Memory usage remains stable during extended use
   - Constructable Stylesheets provide memory efficiency benefits
   - Large datasets are handled efficiently
   - Lazy loading works when implemented

8. **Cross-browser Compatibility**:
   - Component works across target browsers
   - CSS architecture (Constructable Stylesheets vs fallback) functions correctly
   - Polyfills load and function correctly
   - Feature detection works as expected

9. **Security Testing**:
   - XSS prevention mechanisms work
   - Content sanitization is effective
   - CSP compliance is maintained

---

## Step 4: Documentation and Examples

### Core Documentation Files

1. **README.md - User Documentation**
   - **Component Overview**: Brief description of what the component does and its main features
   - **Quick Start Guide**: Minimal setup to get the component working
   - **Installation Instructions**: Multiple installation methods (script tag, npm, CDN, etc.)
   - **Usage Examples**: Progressive examples from basic to advanced usage
   - **API Documentation**:
     - Complete list of attributes/properties with types and defaults
     - Custom events with payload details and examples
     - Public methods with parameters and return values
     - Slots documentation with examples
   - **Theming Guide**:
     - Complete CSS custom properties reference
     - Built-in theme examples and switching
     - Custom theme creation guide
     - Theme inheritance and cascading rules
   - **Accessibility Features**: WCAG compliance details and usage guidelines
   - **Browser Support**: Compatibility matrix and polyfill requirements
   - **Troubleshooting**: Common issues and solutions
   - **Performance Guidelines**: Best practices for optimal performance
   - **Contributing Guidelines**: How to contribute to the component

2. **Technical Documentation (`readme.technical.md`)**
   - **Architecture Overview**: High-level component design and patterns
   - **Implementation Details**: Key technical decisions and rationale
   - **CSS Architecture**: Detailed explanation of the Constructable Stylesheet Pattern implementation:
     - Why Constructable Stylesheets were chosen over traditional `<style>` elements
     - How the fallback mechanism works for Safari and legacy browsers
     - Performance benefits and memory efficiency of adopted stylesheets
     - Browser feature detection strategy and implementation
   - **Shadow DOM Strategy**: Encapsulation approach and style isolation
   - **Event System**: Custom event design and propagation patterns
   - **Theming Architecture**: CSS custom properties system and inheritance
   - **Performance Optimizations**: Lazy loading, memory management, and efficiency techniques
   - **Security Measures**: XSS prevention, CSP compliance, and safe practices
   - **Testing Strategy**: Test architecture and coverage approach
   - **Known Limitations**: Current constraints and potential improvements
   - **Extensibility Points**: How to extend or customize the component

3. **Formal Specification (`spec.md`)**
   - **Component Purpose**: Formal definition of component goals and scope
   - **Functional Requirements**: Detailed behavioral specifications
   - **API Specification**: Complete interface definition
   - **Accessibility Requirements**: WCAG compliance specifications
   - **Performance Requirements**: Benchmarks and constraints
   - **Security Requirements**: Security model and threat mitigation
   - **Browser Compatibility**: Supported environments and fallbacks
   - **Integration Constraints**: Framework compatibility and limitations
   - **Non-Goals**: Explicitly excluded features and use cases

4. **Migration Guide (`migration-guide.md`)**
   - **From Legacy Solutions**: Step-by-step migration instructions
   - **Breaking Changes**: Version-to-version upgrade guide
   - **Compatibility Shims**: Temporary compatibility layers
   - **Migration Tools**: Automated migration scripts or utilities

### Interactive Examples and Demos

5. **Interactive Demo Page (`examples/demo.html`)**
   - **Component Showcase**: Live examples with real-time property manipulation
   - **Theme Demonstration**: Interactive theme switcher with all built-in themes
   - **Theme Selector UI**: The demo page MUST include a user interface (such as a dropdown, segmented control, or radio buttons) that allows users to select and apply any of the built-in themes (light, dark, forest-green, warm-sunset) in real time. The selected theme should update the component(s) on the page immediately without requiring a reload.
   - **Custom Theme Builder**: Color pickers and CSS custom property editors
   - **Accessibility Demo**: Screen reader testing and keyboard navigation examples
   - **Performance Metrics**: Live performance monitoring and optimization tips
   - **Integration Examples**: Usage with popular frameworks and libraries
   - **Code Playground**: Editable code examples with live preview
   - **Responsive Design**: Mobile and desktop layout demonstrations



### Development and Configuration Files

7. **Package Configuration (`package.json`)**
   - **Metadata**: Name, version, description, keywords
   - **Entry Points**: Main, module, types, and browser fields
   - **Dependencies**: Runtime and development dependencies
   - **Scripts**: Build, test, lint, and development commands
   - **Publishing**: NPM registry configuration and files inclusion
      - **Behavior Note**: The generator SHALL inspect the current project for an existing package.json and merge non-destructively as described in the Project Pre-check. If merging cannot be safely automated, a package.json.generated will be produced and the user will be instructed how to integrate changes.

8. **Development Configuration**
   - **ESLint Configuration (`.eslintrc.js`)**: If present, prefer to add an `extends: ['recommended-component-config']` entry or merge rules; do not overwrite without confirmation.
   - **Prettier Configuration (`.prettierrc`)**: Merge formatting rules when safe, otherwise create a `.prettierrc.generated`.
   - **TypeScript (`tsconfig.json`)** and **Jest (`jest.config.js`)**: Prefer to extend or create companion config files and document required changes in README.
   - **Logging**: Always emit a concise summary of which files were detected, which were merged, which were generated (with `.generated` suffix), and any backups created.

---

## Quick Checklist

- [ ] Clarified and summarized the component description
- [ ] Feature breakdown and requirements
- [ ] Overall summary
- [ ] (After confirmation) Conceptual web component proposal with code and usage example
- [ ] (After implementation) README.md with comprehensive documentation
- [ ] (After implementation) Sample HTML page demonstrating component usage

## Usage

The user's component description will be provided in the context. Analyze the description and follow the steps above to generate the complete project structure, documentation, demo, and tests.
