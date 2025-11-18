---
mode: agent
---

# Complete Test Suite Generator

Orchestrate a three-phase test generation process: **Document → Generate → Validate**

**Phase 1:** Analyze source code and create comprehensive test instructions  
**Phase 2:** Convert test instructions into runnable Jest test files  
**Phase 3:** Execute tests and validate results

---

## Process Overview

### Phase 1: Document Required Tests

1. Analyze the provided JavaScript/TypeScript source file(s)
2. Generate a `test-instruction-<filename>.md` document with:
   - Public API inventory
   - Comprehensive test case descriptions
   - Mock plans for external dependencies
   - Coverage strategy (≥90% statement/branch)

### Phase 2: Generate Test Files

1. Parse the test instruction document
2. Create runnable Jest test files
3. Generate test plan and setup files
4. Report coverage and blockers

### Phase 3: Validate & Execute

1. Run the generated test suite
2. Analyze test failures and categorize them
3. Fix test implementation issues (NOT source code)
4. Document legitimate failures as source code bugs

---

## Input Requirements

**Required:**

- Source code file(s) to test
- Target framework (default: Jest + jsdom for web components, Jest + Node for utilities)

**Optional:**

- Existing test instruction files to use instead of generating new ones
- Custom coverage targets (default: 90% for documentation, 85% for generation)
- Specific test patterns or constraints

---

## Execution Strategy

### Auto-Detection

- **File type:** `.js`, `.ts`, `.jsx`, `.tsx`
- **Module system:** ESM vs CommonJS
- **Runtime:** Node.js vs Browser
- **Framework:** Web Components vs utility functions

### Phase 1 Execution

Use the **Document Required Tests** prompt to:

- Analyze source code structure
- Identify all public APIs
- Plan comprehensive test coverage
- Document external seams and mock requirements

### Phase 2 Execution

Use the **Test Generation Orchestrator** prompt to:

- Parse test instruction documents
- Generate runnable test files
- Create supporting infrastructure
- Report coverage gaps and blockers

### Phase 3 Execution

Execute and validate the generated test suite:

- Run tests using the project's test runner (Jest/npm test)
- Categorize failures: test bugs vs source code bugs
- Fix test implementation issues only
- Document source code bugs without modifying source

---

## Output Structure

```
tests/
├── <component-or-module>/
│   ├── 01-<purpose>—<edge>.test.js
│   ├── 02-<purpose>—<edge>.test.js
│   └── ...
├── TEST_PLAN.md
├── jest.setup.js (if needed)
└── test-instruction-<filename>.md
```

---

## Execution Commands

### Single File Processing

```
Phase 1: Document tests for [filename]
Phase 2: Generate tests from test-instruction-[filename].md
Phase 3: Execute and validate test suite
```

### Batch Processing

```
Phase 1: Document tests for all files in [directory]
Phase 2: Generate complete test suite from all instruction files
Phase 3: Run full test suite and validate results
```

---

## Quality Gates

### After Phase 1

- [ ] All public APIs documented
- [ ] Test cases cover ≥90% of branches
- [ ] External dependencies have mock plans
- [ ] Edge cases and error conditions identified

### After Phase 2

- [ ] All test files compile and run
- [ ] Test structure follows naming conventions
- [ ] Mocks are properly configured
- [ ] Coverage targets are achievable

### After Phase 3

- [ ] Test suite executes successfully
- [ ] Test failures are categorized correctly
- [ ] Test implementation bugs are fixed
- [ ] Source code bugs are documented (not fixed)

---

## Example Workflow

### Input

```javascript
// utils/date-formatter.js
export function formatDate(date, format = "YYYY-MM-DD") {
  if (!date || !(date instanceof Date)) {
    throw new Error("Invalid date provided");
  }
  // ... formatting logic
}

export class ApiClient {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
  }

  async get(endpoint) {
    // ... HTTP logic with retry
  }
}
```

### Phase 1 Output

```markdown
# Test Instructions — date-formatter.js

**Public API Inventory:**

- `formatDate(date, format)` - Date formatting utility
- `ApiClient` class - HTTP client with retry logic

## formatDate(date, format)

**Test Cases:**

- **Test purpose:** Verify standard date formatting
- **Inputs:** Valid Date object, format "YYYY-MM-DD"
- **Expected:** Returns "2023-12-25"
- **Edge-path:** Happy path formatting

- **Test purpose:** Handle invalid date input
- **Inputs:** null, undefined, string, number
- **Expected:** Throws "Invalid date provided"
- **Edge-path:** Input validation guard
```

### Phase 2 Output

```javascript
// tests/date-formatter/01-format-date—happy-path.test.js
describe("formatDate — standard formatting", () => {
  it("formats valid date with default pattern — happy-path", () => {
    const date = new Date("2023-12-25");
    const result = formatDate(date);
    expect(result).toBe("2023-12-25");
  });
});

// tests/date-formatter/02-format-date—input-validation.test.js
describe("formatDate — input validation", () => {
  it("throws error for invalid date inputs — input-validation", () => {
    expect(() => formatDate(null)).toThrow("Invalid date provided");
    expect(() => formatDate("not-a-date")).toThrow("Invalid date provided");
  });
});
```

---

## Phase 3: Test Execution & Validation

### Execution Strategy

**Run Tests with Coverage:**

```bash
npm test -- --coverage --verbose    # Always run with coverage and detailed output
npm test -- --coverage --watchAll=false  # For CI environments
jest --coverage --verbose            # Direct Jest execution
```

**Coverage Configuration:**

- Always generate coverage reports
- Include coverage thresholds validation
- Export coverage data for analysis

**Failure Analysis:**

1. **Syntax Errors:** Fix test code immediately
2. **Import/Setup Issues:** Fix test configuration
3. **Logic Failures:** Categorize as test bug vs source bug

### Failure Categorization

#### Test Implementation Bugs (FIX THESE)

- **Syntax errors** in test files
- **Import/export** mismatches
- **Mock configuration** issues
- **Async handling** problems (missing await, wrong Promise handling)
- **DOM setup** issues (missing elements, incorrect selectors)
- **Test isolation** problems (shared state, cleanup issues)

#### Source Code Bugs (DOCUMENT, DON'T FIX)

- **Logic errors** in the source code
- **Missing error handling** in source
- **Incorrect return values** from source functions
- **Unhandled edge cases** in source
- **API contract violations** in source

### Fix Strategy

#### For Test Implementation Bugs

```javascript
// BEFORE (broken test)
it("should format date", () => {
  const result = formatDate("2023-12-25"); // Wrong input type
  expect(result).toBe("2023-12-25");
});

// AFTER (fixed test)
it("should format date", () => {
  const result = formatDate(new Date("2023-12-25")); // Correct input
  expect(result).toBe("2023-12-25");
});
```

#### For Source Code Bugs

```javascript
// Source has bug: doesn't handle null input
export function formatDate(date) {
  return date.toISOString().split("T")[0]; // Will throw on null
}

// Test correctly fails and documents the bug
it("should handle null input gracefully", () => {
  // This test SHOULD fail - it reveals a source code bug
  expect(() => formatDate(null)).not.toThrow();
  // OR expect a specific error handling behavior
  expect(() => formatDate(null)).toThrow("Invalid date provided");
});
```

### Validation Process

#### 1. Execute Test Suite with Coverage

```bash
npm test -- --coverage --verbose 2>&1 | tee test-results.log
```

**Coverage Analysis:**

- Generate HTML coverage report in `coverage/` directory
- Validate against target thresholds (90% statement, 85% branch)
- Identify uncovered lines and branches
- Export coverage data as JSON for further analysis

#### 2. Analyze Failures

For each failing test:

- **Read the error message carefully**
- **Examine the test code for implementation issues**
- **Check if the source code behavior matches expectations**
- **Categorize the failure type**

#### 3. Fix Test Issues

- **Syntax errors:** Fix immediately
- **Mock issues:** Update mock configuration
- **Async issues:** Add proper await/Promise handling
- **Setup issues:** Fix DOM/environment setup

#### 4. Document Source Issues

Create a `SOURCE_BUGS.md` file:

```markdown
# Source Code Issues Found During Testing

## formatDate function

- **Issue:** Doesn't handle null input gracefully
- **Test:** `02-format-date—null-input.test.js`
- **Expected:** Should throw meaningful error
- **Actual:** Throws TypeError: Cannot read property 'toISOString' of null
- **Severity:** Medium - should validate input

## ApiClient.retry method

- **Issue:** Infinite retry loop on network errors
- **Test:** `05-api-client—retry-limit.test.js`
- **Expected:** Should stop after maxRetries
- **Actual:** Continues indefinitely
- **Severity:** High - can cause resource exhaustion
```

### Test Execution Report

Generate a comprehensive report after Phase 3:

```markdown
# Test Execution Report

## Summary

- **Total Tests:** 45
- **Passing:** 38
- **Failing:** 7
- **Test Implementation Bugs Fixed:** 4
- **Source Code Bugs Documented:** 3

## Coverage Results

- **Statement Coverage:** 87% (Target: 90%)
- **Branch Coverage:** 84% (Target: 85%)
- **Function Coverage:** 92% (Target: 90%)
- **Line Coverage:** 89% (Target: 90%)

### Coverage Gaps

- **Uncovered Lines:** 15 lines in error handling paths
- **Uncovered Branches:** 8 branches in edge case conditions
- **Uncovered Functions:** 2 utility functions (marked as internal)

### Coverage Analysis

- **HTML Report:** `coverage/lcov-report/index.html`
- **JSON Data:** `coverage/coverage-final.json`
- **LCOV Format:** `coverage/lcov.info`

## Test Implementation Fixes

1. **Mock configuration** - Fixed IntersectionObserver mock
2. **Async handling** - Added missing await in API tests
3. **DOM setup** - Fixed missing element creation
4. **Import paths** - Corrected relative import paths

## Source Code Issues Found

1. **formatDate null handling** - Medium severity
2. **ApiClient retry logic** - High severity
3. **Menu keyboard navigation** - Low severity

## Recommendations

1. Fix high-severity source bugs before production
2. Add input validation to formatDate function
3. Implement retry limits in ApiClient
4. Add tests for uncovered error handling paths
5. Consider integration tests for complex workflows

## Coverage Threshold Status

- ✅ Function Coverage: 92% (exceeds 90% target)
- ❌ Statement Coverage: 87% (below 90% target)
- ❌ Branch Coverage: 84% (below 85% target)
- ❌ Line Coverage: 89% (below 90% target)
```

---

## Configuration Options

### Coverage Targets & Configuration

**Target Thresholds:**

- **Documentation phase:** 90% statement + branch coverage
- **Generation phase:** 85% achievable coverage
- **Security/A11y:** Include when detected in source

**Jest Coverage Configuration:**

```json
{
  "collectCoverage": true,
  "coverageDirectory": "coverage",
  "coverageReporters": ["text", "lcov", "html", "json"],
  "coverageThreshold": {
    "global": {
      "branches": 85,
      "functions": 90,
      "lines": 90,
      "statements": 90
    }
  },
  "collectCoverageFrom": [
    "src/**/*.{js,ts,jsx,tsx}",
    "!src/**/*.d.ts",
    "!src/**/*.test.{js,ts,jsx,tsx}",
    "!src/**/__tests__/**"
  ]
}
```

**Coverage Analysis:**

- Always run tests with `--coverage` flag
- Generate multiple report formats (HTML, LCOV, JSON)
- Validate against defined thresholds
- Identify specific uncovered lines and branches

### Test Patterns

- **Web Components:** Focus on DOM, attributes, events, accessibility
- **Utilities:** Focus on pure functions, error handling, edge cases
- **APIs:** Focus on HTTP, retries, error responses, timeouts

### Mock Strategies

- **File System:** Mock `fs` operations
- **HTTP:** Mock `fetch`, `axios`, network calls
- **Timers:** Mock `setTimeout`, `Date.now`
- **Browser APIs:** Mock DOM, `window`, storage APIs

---

## Error Handling

### Phase 1 Issues

- **Unparseable code:** Document what's analyzable, note blockers
- **Missing dependencies:** Create mock plans, proceed with assumptions
- **Ambiguous APIs:** Ask clarifying questions, use safe defaults

### Phase 2 Issues

- **Invalid instructions:** Skip problematic tests, document in blockers
- **Missing setup:** Generate minimal setup, note requirements
- **Complex scenarios:** Simplify to essential functionality

---

## Integration Points

### With Existing Tests

- **Merge strategy:** Extend existing suites, don't overwrite
- **Naming conflicts:** Use incremental numbering
- **Setup conflicts:** Merge configurations safely

### With CI/CD

- **Coverage reporting:** Generate coverage-compatible output
- **Test discovery:** Follow Jest/framework conventions
- **Performance:** Optimize for fast feedback loops

---

## Usage Examples

### Basic Usage

```
Generate complete test suite for src/components/menu.js
```

### Advanced Usage

```
Generate test suite for src/utils/ with:
- Coverage target: 95%
- Include security tests
- Mock external APIs
- Generate TypeScript tests
```

### Batch Processing

```
Generate test suites for all components in src/components/
- Skip files with existing comprehensive tests
- Focus on public API coverage
- Include accessibility tests for UI components
```

---

## Success Criteria

### Documentation Quality

- All public APIs identified and documented
- Test cases cover critical paths and edge cases
- Mock plans are specific and implementable
- Coverage strategy is realistic and achievable

### Generated Test Quality

- Tests compile and run successfully
- Test structure is maintainable and readable
- Mocks are deterministic and isolated
- Coverage targets are met or gaps are documented

### Test Execution Quality

- Test suite runs successfully with clear pass/fail results
- Test implementation bugs are identified and fixed
- Source code bugs are documented without modification
- Coverage reports are accurate and actionable

### Overall Workflow

- Process completes without manual intervention
- Output follows project conventions
- Integration with existing codebase is seamless
- Documentation supports future maintenance
- Test failures provide clear guidance for developers
