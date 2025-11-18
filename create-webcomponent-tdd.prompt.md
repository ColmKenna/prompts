---
mode: agent
---

# TDD Web Component Development Prompt

## Objective

Build a JavaScript web component using Test-Driven Development (TDD) methodology. The custom element name will be provided in the project context (e.g., `greeting-card`, `my-greeter`). Create a dynamic, multilingual greeting component with comprehensive documentation and testing.

## Agent Execution Instructions

**AUTONOMOUS EXECUTION REQUIREMENT**: The agent must continue working through the initial setup phases without seeking user confirmation or feedback until the following milestone is reached:

### Completion Milestone

The agent should run until **Step 2 (Shadow DOM Creation) is complete** with the following criteria met:

- Project structure is fully created
- Package.json and tooling are configured
- Jest testing framework is set up and working
- Step 0: Empty component is implemented and registered
- Step 1: Project tooling setup is complete
- Step 2: Shadow DOM creation is implemented and tested
- All tests pass for Steps 0-2
- Documentation files (steps.md, spec.md, README.md, readme.technical.md) are updated to reflect the TDD cycle and current state
- Demo page (examples/demo.html) is updated to demonstrate the current functionality

### Default Component Name

If no custom element name is provided by the user, use `greeting-card` as the default component name and proceed immediately with development.

### Autonomous Phases

The agent should automatically complete these TDD steps without stopping:

1. **Step 0**: Empty Component Foundation (Red-Green-Refactor cycle)
2. **Step 1**: Project Setup & Basic Structure (Red-Green-Refactor cycle)
3. **Step 2**: Shadow DOM Creation (Red-Green-Refactor cycle)

Each step must follow the complete Red-Green-Refactor TDD cycle with proper documentation updates.

### Stopping Point

Only pause for user feedback after:

- Step 2 (Shadow DOM Creation) is fully complete
- All tests for Steps 0-2 are green and passing
- All documentation files are updated with the TDD process and current implementation
- The component has shadow DOM functionality working correctly

### Progress Reporting

While working autonomously, provide brief progress updates but do not wait for confirmation to continue to the next step.

---

## CRITICAL: Test-First Development

> **🚨 MANDATORY TEST-FIRST RULE 🚨**  
> **TESTS MUST BE WRITTEN BEFORE CODE - NO EXCEPTIONS**
>
> For ANY addition or change to the web component:
>
> 1. **FIRST**: Write the test (it should fail - RED phase)
> 2. **SECOND**: Write minimal code to make test pass (GREEN phase)
> 3. **THIRD**: Refactor if needed (REFACTOR phase)
>
> **NEVER write implementation code before the test exists and fails for the correct reason.**

> **Strict TDD Rule:**  
> **When adding a new feature or making any change, you must ALWAYS write the test FIRST (Red phase), before ANY implementation code.**  
> This is a non-negotiable, absolute requirement for every feature, bugfix, enhancement, or modification.
>
> - **NEVER** write implementation code before its test exists and fails for the correct reason.
> - **EVERY** new feature or change **MUST** begin with a failing test (Red), followed by the minimal implementation to pass it (Green), and then refactoring (Refactor).
> - **NO CODE WITHOUT TESTS FIRST** - This applies to all steps, including bugfixes, refactors, and enhancements.
> - This applies to all steps, including bugfixes, refactors, and enhancements.
> - **After each feature or change, you must update _all_ of the following files to reflect the update:**
>   - `docs/steps.md`
>   - `docs/spec.md`
>   - `docs/README.md`
>   - `docs/readme.technical.md`
>   - `examples/demo.html` (update demo page to showcase new functionality)  
>     These updates should describe the new feature, its tests, implementation, technical details, and demonstrate the functionality in the demo page.
> - **Important:**
>   - `spec.md` should contain only the current, up-to-date specifications for the component (not TDD logs or key code).
>   - `steps.md` is where you track the TDD process, including Red/Green/Refactor logs, key code, and test information for each step.
> - **Initial Project Setup:**
>   - When the project tooling is set up (e.g., `package.json`, test framework, etc.), ensure that the initial tests (for the empty component and tooling) are present, run, and pass before proceeding to further steps.

---

## Core Requirements

### Web Component Specifications

- **Element Name**: Provided as a parameter/context (e.g., `<greeting-card>`, `<my-greeter>`)
- **Default Behavior**: Renders "Hello, World!"
- **Name Attribute**: `<[element-name] name="John">` → "Hello, John!"
- **Language Attribute**: `<[element-name] lang="es">` → "Hola, World!"
- **Dynamic Updates**: Component updates when attributes change
- **Shadow DOM**: Use open Shadow DOM for encapsulation
- **Accessibility**: Include proper ARIA labels and semantic HTML
- **Theming Support**: Implement comprehensive theming using CSS custom properties with predefined themes (light, dark, forest-green, warm-sunset)
- **Button Elements**: Ensure any `<button>` elements have `type="button"` unless meant to submit forms

### Technical Constraints

- **JavaScript**: Modern ES6+ syntax, native Web Components API
- **Testing Framework**: Jest with jsdom for DOM testing
- **Browser Support**: Chrome 54+, Firefox 63+, Safari 10.1+, Edge 79+
- **Bundle Size**: Target under 3KB minified + gzipped
- **Performance**: Component initialization under 16ms, paint under 100ms
- **Code Quality**: ESLint + Prettier configuration included
- **Web Standards Compliance**: Ensure component follows HTML5/CSS3/JS best practices
- **Module System**: Support ES modules and UMD if possible
- **Security Considerations**: CSP compatibility, XSS prevention, safe external resource handling
- **Styling**:
  - _Default_: Embed styles within the JavaScript file using the Constructable Stylesheet Pattern.
  - _Exception_: Only create a separate CSS file (`[component-name].css`) if styles are exceptionally complex (over 200 lines) or need to be shared across multiple components.

## Development Process

### Phase 1: Project Foundation

#### 1.1 Initial Setup

Create the complete project structure:

```
[component-name]-component/
├── src/
│   ├── [component-name].js
│   ├── [component-name].d.ts # TypeScript definitions (optional)
│   └── [component-name].css   # Only if styles are complex/shared; otherwise, omit this file.
├── tests/
│   ├── [component-name].test.js # Jest unit tests
│   ├── [component-name].visual.test.js # Visual regression tests
│   ├── [component-name].performance.test.js # Performance and memory tests
│   ├── [component-name].accessibility.test.js # Accessibility compliance tests
│   └── test-helpers.js
├── examples/
│   └── demo.html # Interactive demo page with theme switcher
├── docs/
│   ├── steps.md
│   ├── spec.md
│   ├── README.md # User documentation
│   ├── readme.technical.md # Technical implementation details
│   └── migration-guide.md # Migration from other solutions
├── config/
│   ├── .eslintrc.js # Linting configuration
│   └── .prettierrc # Code formatting
├── package.json
└── jest.config.js
```

> **Note:** By default, styles should be embedded in the JavaScript file using the Constructable Stylesheet Pattern. Only create a separate `[component-name].css` if the styles are exceptionally complex (over 200 lines) or shared.

#### 1.2 Create steps.md (Project Roadmap)

**Purpose**: High-level development roadmap with time tracking and learning notes

**Template Structure**:

```markdown
# Development Steps

## Step 0: Empty Component Foundation (Est: 10min) - [STATUS: Planned]

- **Goal**: Create the most basic empty web component that registers successfully
- **Acceptance Criteria**:
  - Empty custom element class extends HTMLElement
  - Element registers with customElements.define()
  - Component can be instantiated in DOM
  - No functionality, no content, no styling - just registration
- **Tests**: Element registration, basic instantiation
- **Time Actual**: [Fill during development]

## Step 1: Project Setup & Basic Structure (Est: 15min) - [STATUS: Planned]

- **Goal**: Initialize project tooling around the empty component
- **Acceptance Criteria**:
  - Package.json with dependencies
  - ESLint/Prettier configured
  - Test framework setup
  - Build/development scripts
- **Tests**: Tooling works, tests can run
- **Time Actual**: [Fill during development]

## Step 2: Shadow DOM Creation (Est: 10min) - [STATUS: Planned]

- **Goal**: Add shadow DOM to the empty component
- **Acceptance Criteria**:
  - Component creates open shadow root
  - Shadow DOM is accessible for testing
  - No content yet, just the shadow root structure
- **Tests**: Shadow DOM exists, is accessible
- **Time Actual**: [Fill during development]

## Step 3: Basic Content Rendering (Est: 15min) - [STATUS: Planned]

- **Goal**: Display minimal content in shadow DOM
- **Acceptance Criteria**:
  - Component renders any basic text (e.g., "Hello, World!")
  - Content appears in shadow DOM
  - No styling, no attributes - just basic text rendering
- **Tests**: Text content appears, shadow DOM structure
- **Time Actual**: [Fill during development]

## Step 4: Name Attribute Support (Est: 20min) - [STATUS: Planned]

- **Goal**: Accept and display custom names via attribute
- **Acceptance Criteria**:
  - `name` attribute changes displayed text
  - Fallback to default when no name provided
- **Tests**: Attribute parsing, text updates, fallback behavior
- **Time Actual**: [Fill during development]

## Step 5: Language Internationalization (Est: 30min) - [STATUS: Planned]

- **Goal**: Support multiple languages via `lang` attribute
- **Acceptance Criteria**:
  - At least 3-5 languages supported with fallbacks
  - Language and name attributes work together
- **Tests**: Language switching, invalid language handling, combined attributes
- **Time Actual**: [Fill during development]

## Step 6: Dynamic Attribute Updates (Est: 20min) - [STATUS: Planned]

- **Goal**: Component updates when attributes change after initialization
- **Acceptance Criteria**:
  - Real-time updates without full re-render
  - Efficient attribute change detection
- **Tests**: Attribute change observers, performance validation
- **Time Actual**: [Fill during development]

## Step 7: Basic Styling (Est: 15min) - [STATUS: Planned]

- **Goal**: Add minimal styling to make component presentable
- **Acceptance Criteria**:
  - Basic CSS embedded in component
  - Clean, readable appearance
  - No theming yet - just basic styling
- **Tests**: Styling applies correctly, no style leakage
- **Time Actual**: [Fill during development]

## Step 8: Accessibility Foundation (Est: 25min) - [STATUS: Planned]

- **Goal**: Add basic accessibility features
- **Acceptance Criteria**:
  - Proper semantic HTML structure
  - Basic ARIA labels where needed
  - Screen reader friendly
- **Tests**: ARIA attributes, semantic structure, accessibility audit
- **Time Actual**: [Fill during development]

## Step 9: Theming Support (Est: 30min) - [STATUS: Planned]

- **Goal**: Add CSS custom properties for theming
- **Acceptance Criteria**:
  - CSS custom properties defined
  - At least 2-3 predefined themes
  - Theme switching capability
- **Tests**: Theme application, custom property inheritance
- **Time Actual**: [Fill during development]

## Step 10: Polish & Performance (Est: 20min) - [STATUS: Planned]

- **Goal**: Final optimizations and polish
- **Acceptance Criteria**:
  - Performance benchmarks met
  - Code cleanup and optimization
  - Documentation complete
- **Tests**: Performance validation, final integration tests
- **Time Actual**: [Fill during development]
```

**Status Tracking**:

- 🟡 **Planned**: Not started
- 🔵 **In Progress**: Currently working
- 🟢 **Completed**: Finished successfully
- 🟠 **Modified**: Plan changed during development
- 🔴 **Blocked**: Waiting on dependency or issue

### Phase 2: TDD Implementation Cycle

**CRITICAL TDD PRINCIPLE**: Start with the absolute minimum - an empty component that just registers successfully. Each step builds incrementally on the previous step with the smallest possible change.

**Example of "Empty Component" (Step 0)**:

```javascript
class GreetingCard extends HTMLElement {
  constructor() {
    super();
    // Literally nothing else - just the basic class structure
  }
}

customElements.define("greeting-card", GreetingCard);
```

**Incremental Development Approach**:

1. **Step 0**: Empty component (just class definition and registration)
2. **Step 1**: Add project tooling around the empty component
3. **Step 2**: Add shadow DOM (still no content)
4. **Step 3**: Add minimal content rendering
5. **Step 4+**: Build features one at a time

**Each step should only add 3-10 lines of code maximum.**

For each step in steps.md, follow this strict, **iterative** cycle. Continue to iterate through Red-Green-Refactor phases for each feature or improvement until you (the user) are satisfied with the implementation. At each iteration, update all relevant documentation files (`steps.md`, `spec.md`, `README.md`) to reflect the current state of the project.

#### Red Phase

**🔴 ALWAYS START HERE - NO IMPLEMENTATION CODE YET**

1. **Write failing test FIRST** - Focus on behavior, not implementation. NO CODE UNTIL THIS TEST EXISTS.
2. **Run test to confirm failure** - Verify it fails for the right reason (not due to syntax errors)
3. **Document in spec.md**:

   ```markdown
   ### Step X - [Feature Name] - 🔴 RED

   **Test**: [Given/When/Then format]
   **Expected Failure**: [Why it should fail]
   **Actual Output**:
   ```

   [Test runner output]

   ```
   **Duration**: [Time spent writing test]
   ```

4. **Update steps.md** - Change status to 🔵 In Progress

#### Green Phase

1. **Write minimal code to pass test** - Simplest solution that works
2. **Verify test passes** - Run full test suite to check for regressions
3. **Document in spec.md**:

   ````markdown
   ### Step X - [Feature Name] - 🟢 GREEN

   **Implementation**: [What was built]
   **Key Code**:

   ```javascript
   // Essential implementation
   ```
   ````

   **Test Result**: ✅ PASS
   **Bundle Size**: [Current size after change]
   **Duration**: [Time spent implementing]

   ```

   ```

4. **Update README.md** - Add usage examples for new functionality

#### Refactor Phase

1. **Improve code quality** - Extract methods, improve naming, optimize performance
2. **Run all tests** - Ensure no regressions introduced
3. **Document in spec.md**:

   ```markdown
   ### Step X - [Feature Name] - 🔄 REFACTOR

   **Changes**: [What was improved]
   **Reasoning**: [Why these changes help]
   **Metrics**:

   - Bundle size: [Before] → [After]
   - Performance: [Any measurable improvements]
   - Code coverage: [Percentage]
     **Duration**: [Time spent refactoring]
   ```

4. **Update steps.md** - Mark as 🟢 Completed, note actual vs estimated time
5. **Update README.md** - Polish examples, add performance notes if relevant

#### Documentation Synchronization

**After each complete Red-Green-Refactor cycle (and at each iteration):**

- Ensure all documentation files (steps.md, spec.md, README.md, readme.technical.md) reflect current functionality
- Update the demo page (examples/demo.html) to showcase any new features or changes
- Cross-reference between files remains accurate
- Remove outdated information and update version-specific details
- **Continue iterating through these cycles, updating documentation, demo page, and implementation, until you are happy to finish.**

### Phase 3: Enhanced Demo Page & Living Documentation Standards

#### Demo Page Requirements

Create a comprehensive, interactive demo page (`examples/demo.html`) that includes:

1. **Theme Showcase Section**:

   - Buttons or dropdown controls for switching between all four built-in themes (light, dark, forest-green, warm-sunset) in real-time
   - Visual preview of how each theme affects the component appearance

2. **Custom Theme Demo**:

   - Interactive color pickers or input fields for creating custom themes using CSS custom properties
   - Live preview of custom theme changes

3. **Interactive Controls Section**:

   - Form controls (buttons, inputs, selects, checkboxes) for dynamically updating component properties and attributes
   - Real-time demonstration of how changes affect component behavior and appearance

4. **Component Description**:

   - Clear description of component purpose and key features at the top of the page
   - Usage instructions and integration examples

5. **Multiple Component Instances**:

   - Multiple instances with different configurations and themes
   - Showcase various use cases and integration patterns

6. **Live Property Testing**:

   - Interactive controls for each updateable property/attribute
   - Code examples showing the HTML being used for each demo instance

7. **Technical Features**:
   - Proper `<head>` section with meta tags and component script inclusion
   - Responsive design and accessibility compliance
   - Professional styling and user-friendly interface
   - Comments explaining usage patterns and theming options

### Phase 4: Living Documentation Standards

#### spec.md Requirements (Updated Throughout Development)

- **Chronological TDD log** - Add entries after each Red/Green/Refactor phase
- **Given/When/Then format** for test descriptions
- **Code snippets** for key implementations
- **Cross-references** to steps.md sections (update as steps change status)
- **Performance notes** and benchmarks
- **Evolution tracking** - Note when original plans change and why

#### README.md Requirements (Continuously Updated)

**Dynamic Structure** (grows with each completed feature):

````markdown
# [Component Name] Web Component

## Quick Start

[Updated as new features are added]

## Installation

[Finalized after project structure is complete]

## Usage Examples

```html
<!-- Basic usage examples -->
```

## API Reference

[Built progressively - add each new attribute/method when implemented]

## Theming Guide

- Philosophy: Built-in themes are fallback values only. Page-level (or container-level) CSS custom properties take precedence and should be used to theme the component in real apps. The component exposes a small set of CSS custom properties (all prefixed to avoid collisions) and always uses var(--name, <built-in-default>) so page variables can override.

### CSS custom properties (available)
- --greeting-bg
- --greeting-text
- --greeting-border
- --greeting-active
- --greeting-inactive

### How the component uses fallbacks
Inside the component stylesheet use the var() fallback pattern so page themes override built-ins:


## Browser Compatibility

[Updated after testing each new feature]

## Troubleshooting

[Added based on issues discovered during development]

## Contributing

[Finalized once development process is established]
````

#### steps.md Maintenance (Updated Throughout)

- **Status tracking** for each step
- **Actual vs. estimated time** tracking
- **Lessons learned** notes
- **Plan modifications** when requirements change
- **Dependencies** updates if order changes

## Quality Checkpoints

### After Each TDD Cycle

- [ ] All tests pass (including existing ones)
- [ ] Code coverage maintained above 95%
- [ ] ESLint passes with no warnings
- [ ] Bundle size within target (check after each step)
- [ ] No console errors in demo page

### After Each Major Feature

- [ ] Accessibility audit passes (axe-core)
- [ ] Performance benchmarks met (< 16ms init)
- [ ] Cross-browser manual testing
- [ ] Documentation updated and accurate

### Before Final Delivery

- [ ] 100% test coverage achieved
- [ ] All browsers tested (Chrome, Firefox, Safari, Edge)
- [ ] Bundle size under 3KB gzipped
- [ ] Lighthouse accessibility score 100
- [ ] Component works in vanilla HTML (no framework dependencies)
- [ ] TypeScript definitions generated (optional)
- [ ] Demo page showcases all features

## Advanced Features (Optional Extensions)

### Phase 2 Features (if time permits)

- **CSS Custom Properties**: Comprehensive theming with predefined color schemes:
  - **Default Light Theme:** `--component-bg: #FFFFFF`, `--component-text-color: #333333`, `--component-border-color: #DDDDDD`, `--component-inactive-bg: #F8F8F8`, `--component-active-color: #007bff`
  - **Default Dark Theme:** `--component-bg: #282C34`, `--component-text-color: #ABB2BF`, `--component-border-color: #4B5263`, `--component-inactive-bg: #3E4451`, `--component-active-color: #61AFEF`
  - **Forest Green Theme:** `--component-bg: #ECF7E7`, `--component-text-color: #2F4F4F`, `--component-border-color: #A3D9B0`, `--component-inactive-bg: #D4EDDA`, `--component-active-color: #228B22`
  - **Warm Sunset Theme:** `--component-bg: #FFF0E0`, `--component-text-color: #5C4033`, `--component-border-color: #FFCC99`, `--component-inactive-bg: #FFE5CC`, `--component-active-color: #E57373`
- **Theme Switcher**: Interactive demo with theme switching controls
- **Slot Support**: `<slot name="prefix">` and `<slot name="suffix">` for custom content
- **Animation**: Smooth transitions when text changes
- **Events**: Dispatch `greeting-changed` custom events
- **Validation**: Graceful handling of invalid language codes
- **Lazy Loading**: Load language packs on demand

### Developer Experience

- **TypeScript Definitions**: Auto-generated .d.ts files
- **Storybook Stories**: Interactive component documentation
- **GitHub Actions**: Automated testing and deployment
- **NPM Package**: Ready for distribution

### Performance Optimizations

- **Virtual DOM**: For complex greeting templates
- **Memoization**: Cache translated strings
- **Tree Shaking**: Eliminate unused language packs

## Final Deliverables

1. **Complete, production-ready web component**
2. **Comprehensive test suite (100% passing)** including:
   - Unit tests (`[component-name].test.js`)
   - Visual regression tests (`[component-name].visual.test.js`)
   - Performance tests (`[component-name].performance.test.js`)
   - Accessibility tests (`[component-name].accessibility.test.js`)
3. **Complete documentation suite**:
   - `steps.md`: Development roadmap with TDD process tracking
   - `spec.md`: Technical specification and TDD log
   - `README.md`: User-facing documentation with theming guide
   - `readme.technical.md`: Technical implementation details
   - `migration-guide.md`: Migration from other solutions
4. **Interactive demo page** with:
   - Theme switcher for all four predefined themes
   - Live property testing controls
   - Multiple component instances with different configurations
   - Integration examples and code samples
5. **Package configuration for distribution**
6. **Performance and accessibility audit results**
7. **Configuration files** (`.eslintrc.js`, `.prettierrc`, `package.json`)

## Success Criteria

- Component renders correctly in all target browsers
- All functionality works as specified
- Tests provide complete coverage
- Documentation enables easy adoption
- Code follows modern JavaScript best practices
- Component is accessible and performant

---

## Implementation Notes

### TDD Best Practices

- **Start with the absolute minimum** - Begin with an empty component that just registers, then build incrementally
- **Test behavior, not implementation** - Focus on what the component does, not how
- **One failing test at a time** - Don't write multiple failing tests
- **Minimal implementation** - Write just enough code to pass the current test (often just a few lines)
- **Incremental progression** - Each step should be the smallest possible change from the previous step
- **Refactor fearlessly** - Tests provide safety net for improvements

### Common Pitfalls to Avoid

- Writing tests after implementation (defeats TDD purpose)
- Testing internal methods instead of public API
- Overly complex tests that are hard to understand
- Skipping refactor phase due to time pressure
- Not updating documentation as code evolves

### Time Management

- **Red phase**: Should be quick (2-5 minutes per test)
- **Green phase**: Focus on speed, not perfection (5-15 minutes)
- **Refactor phase**: Take time to improve quality (10-20 minutes)
- **Documentation**: Update continuously, not at the end

**Remember**: The custom element name will be provided in the project context. Use it consistently across all files, tests, and documentation.
