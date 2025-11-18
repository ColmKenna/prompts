---
mode: agent
---

# Document Required Tests

## Task

From the provided JavaScript source, produce a comprehensive, bulleted list of test cases for all public functions to achieve ≥90% statement and branch coverage. Do not write any test code—only detailed descriptions.

### Coverage Target

- **≥90% statement coverage** at the file level
- **≥90% branch coverage** for each public function
- Document any unreachable code and justify exclusion from coverage metrics

## Runtime & Detection Rules

### Runtime

Node.js (assume current LTS unless the file indicates otherwise).

### Module System Detection

Mixed; detect per file and state what you detected up front:

- **ESM:** Look for `export`, `export default`, `import` statements
- **CommonJS:** Look for `module.exports`, `exports.`, `require()` calls
- **Mixed:** Document both patterns found and their interaction

### What Counts as "Public"

Anything usable from outside the file:

- Exports
- Public class methods
- Functions returned by exported factories
- Re-exports

### Traversal Scope (single-file context, follow-reachable)

- Parse static imports/requires/re-exports in the provided file
- If the referenced source is present in context, analyze its exports to resolve what this file exposes or depends on
- If not present, treat it as an external seam: don't infer internals; provide a Mock plan (see below)
- Handle dynamic imports conservatively with specific test scenarios:
  - Module not found errors
  - Version/shape mismatches
  - Conditional imports based on environment
  - Import timing and async behavior

## Output Format — Strict (Markdown file only)

Produce exactly one Markdown document with no extra commentary before/after.

### Filename Convention

`test-instruction-<filename>.md`

Where `<filename>` = basename of the provided JS file without extension, lowercased; non-alphanumeric → `-`; collapse repeated `-`.

**Examples:**

- `math.js` → `test-instruction-math.md`
- `user_service.util.js` → `test-instruction-user-service-util.md`
- `index.js` → `test-instruction-index.md`

### Top-of-file Structure (in this order)

```markdown
# Test Instructions — <original file name>

Assumptions & detections: runtime, module system, framework (Jest unless file indicates otherwise), inferred types, ambiguities.

Public API inventory: bulleted list of all public functions/methods considered (including any re-exports resolved from context).

External seams & mock plans: one sub-section per unresolved dependency.

Per-function sections (see template below).

Coverage checklist (file-level): summarize remaining edge areas, if any.

Open questions (only if needed): ≤3 concise questions; proceed with best-effort assumptions regardless.
```

## Per-function Section Template

Repeat for each public function:

```markdown
## <functionName>(<key params>)

Summary: one-sentence purpose of the function (from code/docs).

Test cases: (each case is a bullet block with this exact sub-structure)

- **Test purpose:** one-sentence intent
- **Inputs / preconditions:** args + state + environment; include mocks/stubs needed
- **Expected outcome:** return value / thrown error / observable side effects (state, FS/network calls, logs)
- **Edge-path exercised:** name the guard/branch/condition (e.g., "null-guard → error branch", "retry after 429", "fallback cache hit")

**Edge-case checklist (function-level):**

- **Input Validation:** invalid types, null/undefined, empty vs singleton
- **Boundaries:** min/max & off-by-one, large inputs/arrays
- **Data Handling:** Unicode/emoji, floating-point quirks
- **Temporal:** time/date/DST, timers/clock skew
- **Concurrency:** reentrancy, race conditions
- **State Management:** immutability vs mutation
- **Environment:** randomness, env vars
- **Node.js Specifics:** buffers, streams, path/OS separators
- **Browser Compatibility:** Cross-browser behavior differences, polyfill requirements, feature detection
- **Accessibility:** ARIA roles/attributes, keyboard navigation, screen reader compatibility

**Security Checklist (function-level):**

- **Input Sanitization:** Test with inputs containing special characters (e.g., `<script>`, `'`, `--`) to check for injection vulnerabilities.
- **Information Exposure:** Ensure error messages do not leak sensitive stack traces or internal paths.

**Accessibility Checklist (function-level):**

- **ARIA Compliance:** Verify proper ARIA roles, properties, and states are applied to DOM elements
- **Keyboard Navigation:** Test tab order, focus management, and keyboard-only interaction patterns
- **Screen Reader Support:** Validate semantic markup and accessible names/descriptions
- **Color/Contrast:** Ensure functionality doesn't rely solely on color or visual indicators
```

## Coverage Strategy

Apply systematically with prioritization:

### Priority 1 (Critical for 90% coverage)

- **Happy paths** - Core functionality working as expected
- **Error paths** - Exception handling and error conditions
- **Boundaries** - Edge values, limits, and transitions

### Priority 2 (Important for robustness)

- **Optional/default params** - Parameter variations and defaults
- **Async behavior** - Promise resolve/reject/timeout/cancellation
- **Integration seams** - External dependencies with mock plans

### Priority 3 (Nice-to-have for comprehensive testing)

- **Side effects** - Idempotence, cleanup, mutation prevention
- **Complex scenarios** - Multi-step workflows, state transitions

### Handling Complex Functions

For functions with multiple responsibilities:

1. Break down into logical units of behavior
2. Test each responsibility independently
3. Test interactions between responsibilities
4. Focus on the most critical paths first

## Mocking Guidance

Required for every external seam:

### What to Stub

- **File System:** `fs.readFile`, `fs.writeFile`, `fs.access`, `path` operations
- **HTTP/Network:** `fetch`, `axios`, `http.request`, WebSocket connections
- **Database:** Client connections, query methods, transaction handling
- **Timers:** `setTimeout`, `setInterval`, `Date.now`, `process.hrtime`
- **Process/Environment:** `process.env`, `process.argv`, `process.exit`
- **Crypto/Random:** `Math.random`, `crypto.randomBytes`
- **Browser APIs:** `window`, `document`, `localStorage`, `sessionStorage`, `navigator`

### Scenarios to Simulate

- **Success Cases:** Normal operation, expected data formats
- **Network Issues:** Slow/timeout, connection refused, DNS failures
- **Retry Logic:** Exponential backoff, max retry limits
- **Permission/Auth:** Access denied, expired tokens, rate limiting
- **Data Issues:** Partial reads, malformed JSON, encoding problems
- **HTTP Errors:** 4xx client errors, 5xx server errors
- **System Issues:** Clock jumps, memory pressure, disk full
- **Randomness:** Fixed-seed for deterministic testing
- **Browser Quirks:** Safari date parsing, IE11 compatibility, Chrome/Firefox differences

### Observables to Assert

- **Call Patterns:** Calls made/not made, call order, parameter values
- **Retry Behavior:** Retry count, backoff timing, circuit breaker state
- **Resource Management:** Cleanup, connection pooling, memory leaks
- **Side Effects:** Logs/metrics, file system changes, network calls
- **Accessibility Impact:** ARIA attribute changes, focus state updates, screen reader announcements

## Information Handling

If information is missing: ask up to 3 concise questions at the end under "Open questions", then proceed with justified assumptions.

## Testing Framework Considerations

While avoiding code generation, consider Jest-specific patterns:

- **Async Testing:** Promise-based vs callback-based testing approaches
- **Mock Patterns:** `jest.fn()`, `jest.spyOn()`, module mocking strategies
- **Timer Mocking:** `jest.useFakeTimers()` for time-dependent code
- **Snapshot Testing:** When appropriate for complex object outputs

## Validation Checklist

Before finalizing test instructions, verify:

- [ ] All exported functions/methods identified and documented
- [ ] Each function has test cases covering main execution paths
- [ ] Error conditions and edge cases addressed
- [ ] External dependencies have mock plans
- [ ] Coverage strategy prioritizes critical functionality
- [ ] Dynamic imports and module loading scenarios covered
- [ ] Async behavior (promises, callbacks, streams) properly tested
- [ ] Browser-specific quirks and compatibility issues addressed
- [ ] Accessibility requirements (ARIA, keyboard navigation, screen reader support) validated

## Example Output Structure

```markdown
# Test Instructions — utils.js

**Assumptions & Detections:**

- Runtime: Node.js 18+ (LTS)
- Module system: ESM (export/import statements detected)
- Framework: Jest (default assumption)
- Types: Inferred from JSDoc comments and usage patterns

**Public API Inventory:**

- `formatDate(date, format)` - Date formatting utility
- `validateEmail(email)` - Email validation function
- `ApiClient` class - HTTP client with retry logic

**External Seams & Mock Plans:**

- `node:fs` - Mock file system operations
- `node:http` - Mock HTTP requests and responses

## formatDate(date, format)

Summary: Formats a Date object according to the specified format string.

**Test Cases:**

- **Test purpose:** Verify standard date formatting with common patterns
- **Inputs/preconditions:** Valid Date object, format string "YYYY-MM-DD"
- **Expected outcome:** Returns formatted string "2023-12-25"
- **Edge-path exercised:** Happy path with standard format
```

## Prohibitions

- **No code** • **Don't invent non-exported functions** • **Don't skip branches** • **Don't assume internal implementation details**
