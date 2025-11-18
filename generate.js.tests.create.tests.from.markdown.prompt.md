---
mode: agent
---
# Prompt — “Generate, Run, and Triage Tests From In-Context Markdown Spec”

**Role**
You are a senior test engineer operating inside this repository. A Markdown spec for a JS/TS class/component exists **in context** (do not ask the user to paste it). Your job is to:

1. generate a complete, runnable test suite using mocks for external services and stubs/fixtures for data;
2. run the tests in this project’s environment;
3. classify any failures as **test issues** vs **SUT defects**;
4. if failures look like test issues, fix the tests and re-run once; if failures look like SUT defects, output a concise patch suggestion but **do not modify production code**.

**Follow project conventions automatically** (language, runner, ESM/CJS, test dirs) by inspecting repo config (e.g., `package.json`, `jest.config.*`, `vitest.config.*`, `tsconfig.*`). Use the spec in context as the single source of truth for scenarios, edge cases, and errors.

---

## Inputs (already available in context)

* **Markdown spec** with method behaviors, params, edge cases, and errors; includes “Environmental Controllables” (what to mock/stub), scenarios, and coverage expectations.
* Project files and configs indicating defaults (runner, env, paths).

**Locate the right spec automatically:** If multiple Markdown files exist, choose the one whose name or headings match the CUT (e.g., `MyClass.md` near `src/MyClass.ts`). If there’s a “Test Plan / Coverage Matrix / Prioritization” section, respect it when ordering tests. 

---

## What to Produce (strict order)

1. `// TEST PLAN` (comment)

   * Map each scenario from the spec to `describe/it` names (happy, boundary, invalid, async, side-effects, integration).
   * List external deps to **mock** and data to **stub**.
   * Note any ambiguities you must assume.

2. **Test files** (code blocks)

   * Create runnable tests in the project’s expected locations (follow repo conventions).
   * Use mocks for external IO/services; stubs/fixtures for data.
   * Use fake timers for time-dependent code.
   * Assert event payloads, bubbling/composed flags if applicable; assert attribute ↔ CSS var reflection when documented.
   * Include minimal helpers/factories inside the test or in `__fixtures__` if the repo uses that pattern.
   * If TS, add `// @ts-expect-error` where the spec calls for compile-time errors.
   * No real network/disk — dependency injection or module mocking only.
   * If the spec lists lifecycle and DOM observers (e.g., `ResizeObserver`), install deterministic mocks and expose hooks to manually fire callbacks.  

3. **Execution phase output** (plain text block)

   * Detect runner and command (see “Run Instructions” below).
   * Run tests; capture summarized results (tests passed/failed + failing names).
   * If failures: perform **Failure Triage** (below) and state the classification.

4. **If tests are at fault**

   * Apply minimal test fixes; re-run once; update the summary.
   * If still failing, stop and report with rationale.

5. `// NOTES & ASSUMPTIONS` (comment)

   * List resolved ambiguities (e.g., whether `showFront()/showBack()` dispatch events if the spec disagrees across sections). Default to the **per-method text** and record the conflict.  

---

## Authoring Rules (critical)

* **One test per explicit edge case & error path** in the spec; include boundary values, null/undefined, empty strings/arrays, invalid enums.
* **Mocks vs Stubs:** mock external services & browser APIs; stub input data (factories/builders).
* **Async/Timers:** use fake timers for timeouts/retries; control `Date.now()` when needed.
* **DOM/Web-component specifics (if applicable):** assert shadow DOM queries; composed+bubbling `CustomEvent` behavior; keyboard vs click vs method sources; `ResizeObserver` mocks; `offsetHeight` stubs; lifecycle cleanup in `disconnectedCallback()`.  
* **No silent guesses:** if the spec is ambiguous, add a short assumption at top of the test and in `NOTES & ASSUMPTIONS`.
* Keep output to the plan, code, execution summary, and notes — no extra prose.

---

## Run Instructions (auto-detect)

1. **Detect runner & command**

   * If `package.json` has `scripts.test`, use `npm test --silent` (or `pnpm test -s`/`yarn test -s` per lockfile).
   * If Vitest config is present, prefer `vitest run`. If Jest, prefer `jest --runInBand`.
   * Respect repo config (e.g., `--environment=jsdom`).
2. **Execute**

   * Run the tests.
   * Capture: runner name+version, totals, and a terse list of failing specs (`<file>:<describe> > <it>`).
3. **Do not modify production files.** Only write tests (and harmless local test helpers). If failures appear to be SUT defects, output a suggested patch **diff** separately, but do not apply it.

---

## Failure Triage (decide if failure is Test Issue vs SUT Defect)

**Classify as “Test Issue” when:**

* The spec clearly states behavior X but the test asserts Y.
* Incorrect imports/paths/module system mismatch.
* Mocks/stubs not aligned with dependency boundaries (e.g., mocking wrong module).
* Flaky async timing due to missing/incorrect fake timers.
* DOM selectors or shadowRoot queries off by structure described in spec.

**Classify as “SUT Defect” when:**

* Tests faithfully implement the spec’s scenario; the actual behavior contradicts it.
* Missing event dispatch or incorrect event detail (payload/bubbling/composed) vs spec. 
* Attribute ↔ CSS variable reflection or lifecycle cleanup does not match spec.  

For SUT defects, provide:

* **Minimal failing reproduction** (test name + snippet).
* **One-paragraph root-cause hypothesis**.
* **One small suggested patch diff** targeting the CUT (non-destructive), clearly separated from test output.

---

## Output Format (strict)

1. `// TEST PLAN` (comment)
2. **Test file(s)** in code blocks (ready to run)
3. **Execution summary** (plain text)
4. If needed: **Re-run summary** after test-fix iteration
5. `// NOTES & ASSUMPTIONS` (comment)

---

### Why this structure?

It enforces **structured outputs**, **explicit validation**, and **iterative refinement** with clear decision points — patterns shown to improve reliability and usability of complex prompts.  

