---
mode: agent
---

# TDD Web Component Feature Prompt

## Objective

Enhance an existing JavaScript web component by adding new features or extending existing ones using strict Test-Driven Development (TDD). The agent must preserve current behavior, extend functionality safely, and keep documentation, tests, and demo content in sync with every change.

## Agent Execution Instructions

**AUTONOMOUS EXECUTION REQUIREMENT**: Proceed without user confirmation until the first requested feature is fully implemented and documented.

### Baseline Verification Phase

Complete these tasks before making code changes:

1. **Project Reconnaissance**: Inspect the current project structure, existing component implementation(s), and tooling configuration.
2. **Test Suite Assessment**: Run the full test suite to establish the green baseline; document any failures you did not cause.
3. **Documentation Review**: Read the current docs (`docs/*.md`, `README.md`, `readme.technical.md`) to understand existing capabilities and constraints.
4. **Feature Requirements Extraction**: Derive the exact feature(s) to implement from the task context (user request, docs, issues). If ambiguity remains, enumerate open questions before coding.
5. **Change Journal Entry**: Create or append to `docs/steps.md` with a baseline entry summarizing current state, covering component capabilities, known limitations, and test status.
6. **Checkpoint Creation**: Generate a unique `docs/checkpoint-YYYY-MM-DD.md` file with comprehensive summary of current state

### Development Milestone

Continue autonomously until **Feature Step F1** is complete with the following criteria met:

- A failing test (RED) captures the requested behavior.
- Implementation code (GREEN) makes the new test pass without breaking existing tests.
- Refactoring (REFACTOR) keeps the codebase clean and consistent with existing patterns.
- All tests (existing + new) pass locally.
- Documentation files (`docs/steps.md`, `docs/spec.md`, `docs/README.md`, `docs/readme.technical.md`) reflect the new feature.
- `examples/demo.html` (or equivalent demo) showcases the new functionality.
- If applicable, styles leverage the Constructable Stylesheet pattern or an existing theming system without regressions.

Only pause for user feedback after achieving this milestone (or if a blocking ambiguity requires clarification).

### Feature Iteration Flow

For each additional feature requested, repeat the cycle:

1. Capture requirements.
2. RED: Add/extend tests that fail for the right reason.
3. GREEN: Implement the minimal code.
4. REFACTOR: Improve design, remove duplication.
5. Update docs & demo.
6. Re-run complete test suite.

Provide concise progress updates between cycles, but do not wait for confirmation unless requirements are unclear.

### Default Assumptions

- If the user does not specify the component name, infer it from the existing project structure; otherwise prompt once for clarification before coding.
- Maintain backward compatibility unless explicitly instructed to introduce breaking changes.
- Preserve existing public APIs, styling hooks, and accessibility guarantees.

---

## CRITICAL: Test-First Development

> **🚨 MANDATORY TEST-FIRST RULE 🚨**  
> **TESTS MUST BE WRITTEN BEFORE CODE - NO EXCEPTIONS**
>
> For EVERY feature, bugfix, or enhancement:
>
> 1. **RED** – Write the test that fails for the new behavior.
> 2. **GREEN** – Write the minimal production code to satisfy the test.
> 3. **REFACTOR** – Improve design while keeping tests green.
>
> - Never write implementation code before its corresponding failing test.
> - Run the entire test suite after each GREEN phase to detect regressions immediately.
> - Document each cycle (Red/Green/Refactor, key decisions, test names) in `docs/steps.md`.
> - Keep `docs/spec.md` focused on the current, authoritative specification—remove outdated details.
> - Update `docs/README.md` and `docs/readme.technical.md` with user-facing and technical insights respectively.
> - Refresh `examples/demo.html` so the new feature is discoverable and verifiable via the demo.

---

## Feature Planning Checklist

- [ ] Confirm understanding of requested feature(s) and acceptance criteria.
- [ ] Map required updates across code, tests, styles, and documentation.
- [ ] Identify integration points with existing attributes, methods, events, theming, and accessibility features.
- [ ] Consider Constructable Stylesheet usage for any new styling concerns to keep styling encapsulated and performant.
- [ ] List potential edge cases (invalid input, attribute changes, dynamic updates, i18n implications).

## Implementation Guidelines

### Testing Strategy

- Mirror existing testing patterns (Jest, Testing Library, helpers) for consistency.
- Prefer behavior-focused tests targeting the public API (attributes, properties, events, DOM output).
- Add regression tests for previously reported bugs when relevant.
- Maintain or improve coverage thresholds reported by the project (e.g., Jest coverage gates).

### Coding Standards

- Follow established component architecture (class structure, render pipeline, state management).
- Use Constructable Stylesheets or existing styling utilities when introducing new styles; fall back to template `<style>` only if the project already does.
- Preserve accessibility features (ARIA attributes, keyboard support) and update tests/docs when expanding them.
- Keep bundle size and performance in mind; avoid unnecessary dependencies.

### Documentation & Demo

- `docs/steps.md`: Log each TDD cycle with Red/Green/Refactor notes and code/test references.
- `docs/spec.md`: Update specification to include the new feature and expected behavior.
- `docs/README.md`: Explain the feature to consumers (usage snippet, options, screenshots if applicable).
- `docs/readme.technical.md`: Detail implementation nuances, data flow, patterns (e.g., Constructable Stylesheet usage).
- `examples/demo.html`: Add or update scenarios that make the new capability obvious and testable.

### Quality Gates

After every feature cycle ensure:

- [ ] All tests pass (`npm test`, `npm run lint`, etc.).
- [ ] Accessibility audits (axe, manual checks) remain green.
- [ ] Performance stays within existing budgets (e.g., <16ms render, <3KB gzip if specified).
- [ ] No console warnings/errors in the demo.
- [ ] Versioning or changelog entries are updated if the project tracks them.

---

## Constructable Stylesheet Pattern (Recommended)

When adding styles or themes:

1. Create or reuse a `CSSStyleSheet` instance for new rules.
2. Use `element.shadowRoot.adoptedStyleSheets` to apply styles.
3. Keep styles modular (one sheet per feature/theme) for reusability.
4. Document the pattern so downstream consumers understand how to extend/customize it.

Fallback to inline `<style>` tags only if the project does not support Constructable Stylesheets or must target browsers without support (document the rationale).

---

## Success Criteria

- New feature behaves exactly as specified, with comprehensive automated tests.
- Existing functionality remains intact; no regressions introduced.
- Documentation and demo content accurately reflect the enhanced capabilities.
- Codebase adheres to existing patterns, style guides, and accessibility requirements.
- Performance, bundle size, and theming/theming extensibility remain within project expectations.

**Remember**: Deliver value incrementally, keep the TDD cycle tight, and leave the project in a healthier state than you found it.
