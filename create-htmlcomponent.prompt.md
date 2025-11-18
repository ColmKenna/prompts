---
mode: agent
---
## Project Structure
All component files must be organized in the following structure:
```
/htmlcomponent/componentname/
├── src/
│   ├── component-name.css # Component styles
│   ├── component-name.js # Component logic
│   └── component-name.d.ts # TypeScript definitions (optional)
├── tests/
│   ├── component-name.test.js # Jest unit tests
│   └── component-name.visual.test.js # Visual regression tests
├── examples/
│   └── demo.html # Interactive demo page with theme switcher and HTML setup example
├── docs/
│   ├── README.md # User documentation (includes HTML setup examples)
│   ├── readme.technical.md # Technical implementation details
│   ├── spec.md # Formal specification
│   └── migration-guide.md # Migration from other solutions
├── config/
│   ├── .eslintrc.js # Linting configuration
│   └── .prettierrc # Code formatting
└── package.json # Package metadata and dependencies
```

## Technical Specifications
- **Web Standards Compliance**: Ensure component follows HTML5/CSS3/JS best practices
- **Module System**: Support ES modules and UMD if possible
- **Performance Requirements**: Component lifecycle and memory usage considerations
- **Security Considerations**: CSP compatibility, XSS prevention, safe external resource handling
- **Browser Support**: Target browsers and required polyfills

# Create Plain CSS/JS Component from Description
You are an expert Frontend Developer. Your task is to design and implement a conceptual UI component using plain CSS and JavaScript based solely on a user-provided description.

**Do not create the HTML structure file.**  
Instead, provide clear documentation and examples showing how users should set up the required HTML for the component to function.

---

## Process

### Step 1: Requirement Analysis

1. **Clarify the Component Description**
   - Carefully read the user's description of the desired component.
   - If any aspect is unclear or ambiguous, ask the user for clarification before proceeding. **Ask all clarification questions in a single prompt, covering all uncertainties at once.**
   - If the user's description is clear, do not ask for confirmation before moving on to the next step—proceed automatically.
   - Identify the main purpose, features, and expected behaviors of the component.
   - List any properties/attributes, events, or slots mentioned or implied in the description.

2. **Feature Breakdown**
   - Break down the description into concrete features and requirements.
   - Identify which features require interactivity, dynamic updates, or external data.
   - Note any accessibility, styling, or integration requirements.

3. **Summary**
   - Summarize the intended functionality, appearance, interactivity, and accessibility of the component based on the description.

---

### Step 2: Plain CSS/JS Component Proposal

Once confirmed, propose a conceptual component that encapsulates the described features and behaviors using plain CSS and JavaScript (no frameworks, no Web Components).

**When creating the component files, place them inside a folder named after the component (e.g., `/fancy-button`, `/custom-card`) in the workspace.** This includes the main CSS and JS files (and any related assets). If the component folder does not exist, create it.

---

## Output Format

- **Suggest a suitable component name** (e.g., "Fancy Button", "Custom Card", "Interactive Nav").
- **Provide a basic HTML snippet** demonstrating how the component would be used in a page.
- **Provide the main CSS file** for the component, demonstrating how the described styles would be applied.
- **Provide a JavaScript file** for the component, demonstrating how the described behaviors/event listeners would be implemented.
- **Always ensure that any `<button>` elements created (in HTML or JavaScript) have `type="button"` unless they are meant to submit a form, to prevent accidental form submission.**
- **Include comments in the code explaining the encapsulation of the described CSS and JS.**
- Show how accessibility features are included or improved.
- **Implement comprehensive theming support using CSS custom properties** with the following color schemes as examples:
  - **Default Light Theme:** `--component-bg: #FFFFFF`, `--component-text-color: #333333`, `--component-border-color: #DDDDDD`, `--component-inactive-bg: #F8F8F8`, `--component-active-color: #007bff`
  - **Default Dark Theme:** `--component-bg: #282C34`, `--component-text-color: #ABB2BF`, `--component-border-color: #4B5263`, `--component-inactive-bg: #3E4451`, `--component-active-color: #61AFEF`
  - **Forest Green Theme:** `--component-bg: #ECF7E7`, `--component-text-color: #2F4F4F`, `--component-border-color: #A3D9B0`, `--component-inactive-bg: #D4EDDA`, `--component-active-color: #228B22`
  - **Warm Sunset Theme:** `--component-bg: #FFF0E0`, `--component-text-color: #5C4033`, `--component-border-color: #FFCC99`, `--component-inactive-bg: #FFE5CC`, `--component-active-color: #E57373`
- **Include a theme switcher in the sample HTML** that allows switching between predefined themes (light, dark, forest-green, warm-sunset) and support for custom theme application via CSS custom properties.

---

### Step 3: Documentation and Sample Implementation

Once the component is created, provide comprehensive documentation and a working example:

1. **README.md File**
   - **Component Overview**: Brief description of what the component does and its main features
   - **Installation Instructions**: How to include the component in a project (HTML setup, CSS, JS inclusion)
   - **Usage Examples**: Multiple code examples showing different ways to use the component, including required HTML structure
   - **API Documentation**: 
     - List all configurable options (data attributes, JS options, etc.)
     - Document any custom events the component emits
     - Describe any public methods available
     - Include default values and data types
   - **Theming Guide**: 
     - Document all CSS custom properties available for theming
     - Provide examples of the four built-in themes (light, dark, forest-green, warm-sunset)
     - Show how to create custom themes using CSS custom properties
     - Include theme switching examples and best practices
   - **Styling Guide**: How to customize the component's appearance (CSS custom properties, classes, etc.)
   - **Accessibility Features**: Document built-in accessibility features and any additional considerations
   - **Browser Support**: List supported browsers and any polyfills needed
   - **Contributing Guidelines**: How others can contribute to the component (if applicable)

2. **Sample HTML Page**
   - Create a complete, working HTML page that demonstrates the component in action
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

---

## Testing Strategy

Create comprehensive test suites to ensure component functionality and robustness:

**Core Test Files:**
- `component-name.test.js`: Main unit tests
- `component-name.visual.test.js`: Visual regression tests
- `component-name.performance.test.js`: Performance and memory tests
- `component-name.accessibility.test.js`: Accessibility compliance tests

**Test Coverage Requirements:**
1. Basic Rendering & Lifecycle
2. State Management
3. User Interactions
4. Content & Slotting
5. Accessibility Testing
6. Theming & Styling
7. Performance Testing
8. Cross-browser Compatibility
9. Security Testing

---

## Advanced Documentation

2. **Technical Documentation (`readme.technical.md`)**
   - Architecture Overview
   - Implementation Details
   - Encapsulation Strategy
   - Event System
   - Theming Architecture
   - Performance Optimizations
   - Security Measures
   - Testing Strategy
   - Known Limitations
   - Extensibility Points

3. **Formal Specification (`spec.md`)**
   - Component Purpose
   - Functional Requirements
   - API Specification
   - Accessibility Requirements
   - Performance Requirements
   - Security Requirements
   - Browser Compatibility
   - Integration Constraints
   - Non-Goals

4. **Migration Guide (`migration-guide.md`)**
   - From Legacy Solutions
   - Breaking Changes
   - Compatibility Shims
   - Migration Tools

## Configuration Files
- `.eslintrc.js`: Code quality and consistency rules
- `.prettierrc`: Code formatting standards
- `package.json`: Metadata, dependencies, scripts

---

## Quick Checklist

- [ ] Technical documentation (readme.technical.md)
- [ ] Formal specification (spec.md)
- [ ] Migration guide (migration-guide.md)
- [ ] Test files (unit, visual, performance, accessibility)
- [ ] Configuration files (.eslintrc.js, .prettierrc, package.json)
- [ ] Performance, security, accessibility, and extensibility documented
- [ ] Configuration files (.eslintrc.js, .prettierrc, package.json)
- [ ] Performance, security, accessibility, and extensibility documented
