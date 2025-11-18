---
mode: agent
---
# Document & Test Existing Web Component
You are an expert Frontend Developer, Technical Writer, and QA Engineer. You will analyze an existing Web Component implementation and generate comprehensive documentation, demos, and test coverage without altering the original component source.

---

## Input Expectations

- One or more JavaScript/TypeScript files containing the Web Component implementation.
- Optional supplementary assets (CSS modules, templates, configuration files) that influence behavior.
- Optional existing documentation or tests for cross-reference.

Always confirm the component's tag name, observed attributes, public methods, events, slots, styling hooks, and configuration patterns directly from the provided source. If any critical details are missing from the input, request the user to supply them before proceeding.

---

## Process Overview

### Step 1: Component Discovery & Analysis
- Parse the component source to catalog:
  - Custom element registration name and class hierarchy
  - Attributes, properties, default values, and reflection semantics
  - Slots (default and named), templates, and light DOM interaction
  - Custom events (names, payload shape, dispatch timing, cancelability)
  - Styling approach (Constructable Stylesheets, Shadow DOM `<style>`, external CSS) and theming hooks
  - Dependencies on external libraries, services, or configuration files
  - Accessibility features (ARIA roles, labelling, focus handling)
  - Security-sensitive logic (HTML rendering, sanitization, user input handling)
  - Performance considerations (lazy loading, virtualization, caching)
- Summarize the component's purpose, primary user flows, and integration touchpoints.

### Step 2: Documentation Suite
Generate the following files under `docs/`:
- `README.md`: Usage-focused overview with installation, quick start, integration snippets, API reference (attributes, properties, methods, events, slots), theming guidance, and troubleshooting.
- `readme.technical.md`: Deep technical design notes covering architecture, state management, styling strategy, event lifecycles, performance optimizations, and security considerations.
- `spec.md`: Formal specification capturing functional requirements, acceptance criteria, API contracts, accessibility mandates, performance targets, and browser support matrix.
- `migration-guide.md`: Guidance for upgrading from prior implementations or integrating into existing systems, including breaking changes and compatibility notes.

### Step 3: Examples & Demos
Create an `examples/` directory containing:
- `demo.html`: Interactive playground showcasing common scenarios, property/attribute toggles, theming selector, accessibility examples, and framework integration samples. Provide inline script to bootstrap the component using the analyzed API.
- Additional example files if the component naturally splits into distinct use cases (e.g., `examples/composed-layout.html`, `examples/form-integration.html`). Each additional example must be justified in a short comment within the file explaining why the scenario is distinct.

### Step 4: Testing Strategy & Assets
Create a `tests/` directory with Jest-based suites:
- `component-name.test.js`: Unit tests covering rendering, lifecycle, attribute/property sync, and core behaviors.
- `component-name.accessibility.test.js`: Automated accessibility checks (ARIA roles, keyboard interaction, focus management, color contrast hooks).
- `component-name.security.test.js`: Security-oriented tests covering sanitization, CSP compliance, and prevention of injection attacks.
- `component-name.visual.test.js`: Visual regression harness instructions (e.g., Jest + @testing-library + image snapshots) with baseline capture guidance.
- `component-name.performance.test.js`: Performance profiling harness (e.g., using jsdom benchmarks or browser automation notes) covering initialization time and memory stability.

Replace `component-name` with the actual kebab-case tag name extracted from the component source. If no tests previously existed, include configuration snippets (e.g., Jest setup) inline within the files or referenced via comments, but do not create config files unless the user explicitly provides context for them.

### Step 5: Quality Gap Audit
- Build a compliance matrix listing Accessibility, Internationalization, Security, Performance, Theming, Browser Support, and Testing.
- Mark each category as `Complete`, `Partial`, or `Missing` based on concrete evidence from the source.
- For any category not marked `Complete`, provide:
  - Specific gaps linked to code locations or observed behaviors
  - Impact assessment (High/Medium/Low)
  - Recommended remediation steps referencing relevant standards (e.g., WCAG, OWASP, performance budgets)

### Step 6: Follow-on Enhancement Prompt
If any category in the audit is `Partial` or `Missing`, craft a ready-to-use follow-up prompt titled **"Enhance Web Component Quality"**. This prompt must:
- Summarize the identified gaps succinctly
- Specify the desired improvements (e.g., "Implement keyboard navigation for menu interactions and verify with automated tests")
- Include the necessary context (file paths, relevant code excerpts, current behavior) so the next agent can immediately implement the fixes
- Request creation or updates to tests/docs affected by the enhancements
Provide the prompt in a fenced code block so the user can copy/paste it directly.

If all categories are `Complete`, state explicitly that no follow-on prompt is required.

---

## Output Packaging
- Present deliverables grouped by directory (`docs/`, `examples/`, `tests/`, `reports/` if you add audit tables) with concise descriptions before the full content.
- Use fenced code blocks with appropriate language hints (`markdown`, `html`, `javascript`, etc.) for each file.
- Precede the audit results with a short narrative summary highlighting major strengths and weaknesses.

---

## Usage Notes
- Do not modify the original component implementation unless the user explicitly requests changes.
- If the provided component relies on external assets that are unavailable, clearly mark the assumptions and suggest mock strategies for documentation/tests.
- When unsure about behavior, ask for clarification before inventing functionality.

---

## Quick Checklist
- [ ] Component analysis completed and summarized
- [ ] Documentation files generated (`README.md`, `readme.technical.md`, `spec.md`, `migration-guide.md`)
- [ ] Demo and example pages created
- [ ] Test suites authored (unit, accessibility, security, visual, performance)
- [ ] Quality audit matrix delivered with remediation guidance
- [ ] Follow-on enhancement prompt provided when gaps exist
