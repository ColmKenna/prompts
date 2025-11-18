---
mode: agent
---
# Generate Web Component Project Structure & Documentation from Existing Files

You are an expert Frontend Developer and Web Component specialist. Your task is to take an existing Web Component implementation (provided as a JavaScript file and optionally a CSS file) and automatically generate a complete, standards-compliant project structure, documentation, and supporting files.

---

## Input

- **JavaScript File**: The main implementation of the custom element (e.g., `my-component.js`)
- **CSS File (Optional)**: The styles for the component (e.g., `my-component.css`) - only if provided separately

**Note**: If no separate CSS file is provided, the system will assume styles are embedded within the JavaScript using the Constructable Stylesheet Pattern, which is the preferred modern approach.

---

## Output

### Step 1: Project Structure Generation

Organize the component into the following structure:

```text
/webcomponent/componentname/
├── src/
│   ├── component-name.js           # Main component implementation (from input)
│   ├── component-name.css          # Component styles (only if provided as separate file)
│   └── component-name.d.ts         # TypeScript definitions (auto-generated)
├── tests/
│   ├── component-name.test.js      # Jest unit tests (auto-generated)
│   └── component-name.visual.test.js # Visual regression tests (auto-generated)
├── examples/
│   └── demo.html                   # Interactive demo page (auto-generated)
├── docs/
│   ├── README.md                   # User documentation (auto-generated)
│   ├── readme.technical.md         # Technical implementation details (auto-generated)
│   ├── spec.md                     # Formal specification (auto-generated)
│   └── migration-guide.md          # Migration from other solutions (auto-generated)
├── config/
│   ├── .eslintrc.js                # Linting configuration (auto-generated)
│   └── .prettierrc                 # Code formatting (auto-generated)
└── package.json                    # Package metadata and dependencies (auto-generated)
```

**CSS Architecture Requirements:**
- **If separate CSS file provided**: Convert to use Constructable Stylesheet Pattern with Safari fallback
- **If no CSS file provided**: Assume styles are already embedded using modern patterns
- **Modernization**: Update legacy `<style>` tag implementations to use Constructable Stylesheets
- **Browser Compatibility**: Ensure fallback support for Safari and older browsers

---

### Step 3: Comprehensive Testing Strategy

#### Unit Testing with Jest

Create comprehensive test suites to ensure component functionality and robustness:

**Core Test Files:**
- `[component-tag-name].test.js`: Main unit tests
- `[component-tag-name].visual.test.js`: Visual regression tests
- `[component-tag-name].performance.test.js`: Performance and memory tests
- `[component-tag-name].accessibility.test.js`: Accessibility compliance tests

**Test Coverage Requirements:**

1. **Basic Rendering & Lifecycle**
   - Component can be added to DOM without errors
   - Shadow DOM is created and attached correctly
   - Component properly cleans up on disconnection
   - Memory leaks are prevented during lifecycle

2. **State Management**
   - Component renders correctly with default properties and attributes
   - Attribute/property reactivity works as expected
   - State changes trigger appropriate re-renders
   - Invalid states are handled gracefully

3. **User Interactions**
   - Simulate user interactions (clicks, key presses, touch events)
   - Verify correct custom events are dispatched with expected details
   - Test keyboard navigation and focus management
   - Validate touch/mobile interactions

4. **Content & Slotting**
   - Named and default slots render content correctly
   - Dynamic slot content updates work properly
   - Slot fallback content displays when appropriate

5. **Accessibility Testing**
   - Essential ARIA attributes are present and correct
   - Keyboard navigation follows expected patterns
   - Screen reader announcements are appropriate
   - Color contrast meets WCAG requirements
   - Focus management works correctly

6. **Theming & Styling**
   - All built-in themes apply correctly
   - Custom theme properties are respected
   - Theme switching works without component re-initialization
   - CSS custom properties cascade properly

7. **Performance Testing**
   - Component initialization time is within acceptable limits
   - Memory usage remains stable during extended use
   - Large datasets are handled efficiently
   - Lazy loading works when implemented

8. **Cross-browser Compatibility**
   - Component works across target browsers
   - CSS architecture (Constructable Stylesheets vs fallback) functions correctly
   - Polyfills load and function correctly
   - Feature detection works as expected

9. **Security Testing**
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

---

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

---

### Development and Configuration Files

7. **Package Configuration (`package.json`)**
   - **Metadata**: Name, version, description, keywords
   - **Entry Points**: Main, module, types, and browser fields
   - **Dependencies**: Runtime and development dependencies
   - **Scripts**: Build, test, lint, and development commands
   - **Publishing**: NPM registry configuration and files inclusion

8. **Development Configuration**
   - **ESLint Configuration (`.eslintrc.js`)**: Code quality and consistency rules
   - **Prettier Configuration (`.prettierrc`)**: Code formatting standards
   - **TypeScript Configuration (`tsconfig.json`)**: Type checking and compilation
   - **Jest Configuration (`jest.config.js`)**: Testing framework setup
   - **Build Configuration**: Bundling and optimization settings

---

## Usage

Paste or add the contents of your component's JS file and optionally CSS file to the context. The system will analyze them and generate the complete project structure, documentation, demo, and tests. If styles are provided separately, they will be converted to use the modern Constructable Stylesheet Pattern with appropriate fallbacks.
