

# Test Generation Orchestrator

Convert **Test Instructions** into **runnable Jest tests** for Web Components.

**Core Process:** Parse → Plan → Generate → Validate → Report

**Key Principles:**
- Generate deterministic, auditable tests
- Proceed with safe assumptions when specs are unclear
- Document blockers with specific follow-up questions
- Target ≥85% coverage without contrived tests

---

## Quick Reference

**Framework:** Jest + jsdom  
**Output:** One test file per test purpose  
**Naming:** `tests/<component>/NN-<purpose>—<edge>.test.js`  
**Coverage:** ≥85% where reasonable, document gaps  

---

## Input Formats Accepted

1. **Markdown** with sections: *Public API*, *Test Cases*, *Edge/Security/A11y*
2. **YAML/JSON** with equivalent structure
3. **Inferred** from component mentions and bullet lists

**Missing sections?** Infer what's possible, document the rest in "Couldn't Complete"

---

## Output Contract

### 1. Test Files
- **Location:** `tests/<component-name>/`
- **Pattern:** `NN-<slug-purpose>—<edge>.test.js`
- **Structure:** Single `describe` + `it` per file
- **Content:** Arrange-Act-Assert with clear spec mapping

### 2. Test Plan (`TEST_PLAN.md`)
- Coverage table: purpose → file → branches covered
- Gap analysis: what's covered vs out-of-scope

### 3. Setup File (if missing)
- **File:** `jest.setup.js`
- **Mocks:** Observers, CustomEvent, getBoundingClientRect

### 4. Blockers Report
- Format: `Item → Why blocked → Default used → Question`

### 5. Summary JSON
```json
{
  "files": ["..."],
  "addedSetup": true|false,
  "coverageTarget": 0.85,
  "blocked": [...]
}
```

---

## Standard Mocks & Utilities

**Always provide these mocks:**

```js
// IntersectionObserver - triggers callback with visible entry
class MockIntersectionObserver {
  constructor(cb) { this._cb = cb; }
  observe() { this._cb([{ isIntersecting: true }]); }
  unobserve() {}
  disconnect() {}
}

// ResizeObserver - stub with trigger helper
class MockResizeObserver {
  observe() {}
  unobserve() {}
  disconnect() {}
}

// MutationObserver - captures records
class MockMutationObserver {
  constructor(cb) { this._cb = cb; }
  observe() {}
  disconnect() {}
  takeRecords() { return []; }
}
```

**CustomElements isolation:** Skip redefining if tag exists, or use random suffix

---

## Test Categories & Patterns

### Registration Tests
```js
it('instantiates and has shadow DOM — happy-path', () => {
  const el = document.createElement('wc-menu');
  document.body.appendChild(el);
  expect(el).toBeInstanceOf(HTMLElement);
  expect(el.shadowRoot).toBeTruthy();
});
```

### Attribute Tests
```js
it('updates on theme changes — attributeChangedCallback', () => {
  const el = document.querySelector('wc-menu');
  el.setAttribute('theme', 'dark');
  // Assert DOM/style changes based on spec
  expect(el.getAttribute('theme')).toBe('dark');
});
```

### Security Tests
```js
it('sanitizes unsafe content — xss-prevention', () => {
  const host = document.createElement('wc-menu');
  host.innerHTML = '<a href="javascript:alert(1)" onclick="alert(2)">Click</a>';
  document.body.appendChild(host);
  
  const a = host.querySelector('a');
  expect(a.getAttribute('href')).not.toMatch(/^javascript:/i);
  expect(a.getAttribute('onclick')).toBeNull();
});
```

### Accessibility Tests
```js
it('supports keyboard navigation — arrow-keys', () => {
  const menu = document.querySelector('wc-menu');
  const firstItem = menu.querySelector('[role="menuitem"]');
  
  firstItem.focus();
  fireEvent.keyDown(firstItem, { key: 'ArrowDown' });
  
  expect(document.activeElement).toBe(/* next item */);
});
```

---

## File Naming Rules

**Component name:** From tag (e.g., `wc-menu`)  
**Order:** 01, 02, 03... (spec appearance order)  
**Purpose slug:** kebab-case, remove stopwords  
**Edge label:** kebab-case, join multiple with `+`  

**Examples:**
- `01-registration—happy-path.test.js`
- `02-attribute-changes—theme-update.test.js`
- `03-keyboard-nav—arrow-keys+enter.test.js`

---

## Default Setup File

**Only emit if project lacks setup:**

```js
// jest.setup.js
class MockIntersectionObserver {
  constructor(cb) { this._cb = cb; }
  observe() { this._cb && this._cb([{ isIntersecting: true }]); }
  unobserve() {}
  disconnect() {}
}

class MockResizeObserver {
  observe() {}
  unobserve() {}
  disconnect() {}
}

class MockMutationObserver {
  constructor(cb) { this._cb = cb; }
  observe() {}
  disconnect() {}
  takeRecords() { return []; }
}

if (typeof window !== 'undefined') {
  window.IntersectionObserver = window.IntersectionObserver || MockIntersectionObserver;
  window.ResizeObserver = window.ResizeObserver || MockResizeObserver;
  window.MutationObserver = window.MutationObserver || MockMutationObserver;
  window.CustomEvent = window.CustomEvent || class extends Event {
    constructor(type, params) {
      super(type);
      this.detail = params?.detail;
    }
  };
}

// Global fallbacks
global.IntersectionObserver = global.IntersectionObserver || MockIntersectionObserver;
global.ResizeObserver = global.ResizeObserver || MockResizeObserver;
global.MutationObserver = global.MutationObserver || MockMutationObserver;
```

---

## Process Details

### 1. Parse
Extract normalized structure:
```json
{
  "component": "wc-menu",
  "publicApi": {
    "attributes": ["theme", "align"],
    "methods": ["open", "close"],
    "events": ["menu-select"]
  },
  "tests": [{
    "purpose": "Verify registration",
    "inputs": ["createElement"],
    "expected": ["HTMLElement instance"],
    "edge": "happy-path"
  }]
}
```

### 2. Plan
- Map purposes to files (1:1)
- Identify required mocks per test
- Plan coverage gaps

### 3. Generate
- Create test files with clear AAA structure
- Include purpose comments from spec
- Use stable selectors (roles/labels preferred)

### 4. Validate
- Check imports resolve
- Verify mocks initialized before use
- Avoid timing dependencies

### 5. Report
- Emit TEST_PLAN.md
- List blockers with specific questions
- Provide summary JSON

---

## Accessibility Guidelines

**Required roles:** `menu`, `menuitem`, `button`  
**Names:** Verify accessible names for interactive elements  
**Keyboard:** Tab entry, Arrow navigation, Enter/Space activation  
**States:** Assert `aria-expanded`, `aria-hidden` changes  

---

## Security Guidelines

**Inject unsafe content:** `<script>`, `onclick`, `href="javascript:"`  
**Assert sanitization:** Dangerous elements/attributes removed  
**No execution:** Verify handlers don't fire for injected content  

---

## Couldn't Complete Format

For each blocked item:
- **Item:** Brief name
- **Why blocked:** Specific missing detail
- **Default used:** Safe assumption made
- **Question:** Exact follow-up needed

**Example:**
- **Item:** Shadow DOM assertions
- **Why blocked:** Spec mentions style updates but no selectors
- **Default used:** Assert attribute presence only
- **Question:** Which CSS classes reflect theme changes?