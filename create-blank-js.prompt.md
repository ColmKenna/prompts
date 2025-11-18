---
mode: agent
---

You are an expert JavaScript developer. Create a new JavaScript application with the following structure:

# Create a Blank JavaScript Project with Jest

This guide walks you through setting up a modern JavaScript project with Jest testing framework, including both Node.js functionality and basic DOM manipulation capabilities.

## 1. Scaffold the project

> Run the following in your project folder.  
> If you don't specify a name, `npm init -y` will use the current folder name.

```bash
npm init -y # generates a bare-bones package.json
```

---

## 2. Add source and test directories

```bash
mkdir src tests
```

---

## 3. Install Jest and dependencies

```bash
npm install --save-dev jest jest-environment-jsdom babel-jest @babel/preset-env
```

> `jest-environment-jsdom` is needed for DOM testing capabilities.  
> `babel-jest` and `@babel/preset-env` are needed for ES module support with Jest.

---

## 4. Configure package.json for testing

Open `package.json` and update the `scripts` section. Also add the `type` field for ES modules:

```json
{
  "name": "my-js-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "jest-environment-jsdom": "^29.0.0",
    "babel-jest": "^29.0.0",
    "@babel/preset-env": "^7.0.0"
  }
}
```

> **Note**: Remove `"type": "module"` if you prefer CommonJS syntax. The `jest-environment-jsdom` package is needed for DOM testing.

---

## 5. Configure Jest for mixed environments

The defaults are fine for most cases, but a minimal `jest.config.js` shows intent:

```js
export default {
  testEnvironment: "node",
  roots: ["<rootDir>/tests"],
  moduleDirectories: ["node_modules", "src"], // lets you do import x from 'utils/foo.js'
};
```

> If you’re using CommonJS, change `export default` to `module.exports =`.

---

## 5.1. Configure Babel for ES modules

Create a `babel.config.js` file to enable ES module transpilation:

```js
export default {
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          node: "current",
        },
      },
    ],
  ],
};
```

> This tells Jest how to transpile ES modules for testing. Without this, Jest may have issues with `import`/`export` statements.

---

## 6. Add your first module & test

**src/sum.js**

```js
export function sum(a, b) {
  return a + b;
}
```

**tests/sum.test.js**

```js
import { sum } from "../src/sum.js";

test("adds 1 + 2 to equal 3", () => {
  expect(sum(1, 2)).toBe(3);
});
```

---

## 7. Add a function to update the DOM using the sum

**src/updateLabel.js**

```js
import { sum } from "./sum.js";

export function updateLabelWithSum(inputId1, inputId2, labelId) {
  const input1 = document.getElementById(inputId1);
  const input2 = document.getElementById(inputId2);
  const label = document.getElementById(labelId);

  if (input1 && input2 && label) {
    const result = sum(Number(input1.value), Number(input2.value));
    label.textContent = `Sum: ${result}`;
  }
}
```

**tests/updateLabel.dom.test.js**

```js
import { updateLabelWithSum } from "../src/updateLabel.js";

test("updates label with sum of inputs", () => {
  document.body.innerHTML = `
    <input id="input1" value="2" />
    <input id="input2" value="3" />
    <label id="label"></label>
  `;

  updateLabelWithSum("input1", "input2", "label");
  expect(document.getElementById("label").textContent).toBe("Sum: 5");
});
```

---

## 8. Run the tests

```bash
npm test
```

You should see Jest discover both test files and run them in their respective environments:

- `sum.test.js` runs in Node.js environment
- `updateLabel.dom.test.js` runs in jsdom environment for DOM testing

### Additional test commands:

```bash
npm run test:watch    # Watch mode - re-runs tests on file changes
npm run test:coverage # Generate coverage report
```

---

## 9. Verify setup is working

Ensure all tests pass by running:

```bash
npm test
```

Expected output should show:

```
PASS tests/sum.test.js
PASS tests/updateLabel.dom.test.js

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
```

If tests fail, double-check:

- File paths and imports are correct
- Jest configuration matches your module system (ES modules vs CommonJS)
- All dependencies are installed

---

## 10. Enable code coverage

Update your `jest.config.js` to include coverage configuration:

```js
export default {
  testEnvironment: "node",
  roots: ["<rootDir>/tests"],
  moduleDirectories: ["node_modules", "src"],
  collectCoverage: true,
  collectCoverageFrom: ["src/**/*.js", "!src/**/*.test.js"],
  coverageDirectory: "coverage",
  coverageReporters: ["text", "lcov", "html"],
};
```

Run coverage reports:

```bash
npm run test:coverage
```

This generates an HTML coverage report in the `coverage/` directory that you can open in your browser.

---

## 11. Add project documentation

Create a `README.md` file in your project root:

**README.md**

````markdown
# JavaScript Project with Jest Testing

A modern JavaScript project setup with Jest testing framework supporting both Node.js and DOM testing.

## Getting Started

### Installation

```bash
npm install
```
````

### Running Tests

```bash
# Run all tests once
npm test

# Run tests in watch mode (re-runs on file changes)
npm run test:watch

# Run tests with coverage report
npm run test:coverage
```

## Project Structure

```
├── src/           # Source code
├── tests/         # Test files
├── coverage/      # Coverage reports (generated)
├── jest.config.js # Jest configuration
└── package.json   # Project dependencies and scripts
```

## Writing Tests

### Node.js Tests

For testing pure JavaScript functions, create test files in the `tests/` directory:

```js
// tests/myFunction.test.js
import { myFunction } from "../src/myFunction.js";

test("should do something", () => {
  expect(myFunction()).toBe(expectedResult);
});
```

### DOM Tests

For testing DOM manipulation, Jest automatically uses jsdom environment for files with DOM-related code:

```js
// tests/domFunction.dom.test.js
import { updateDOM } from "../src/domFunction.js";

test("should update DOM element", () => {
  // Setup DOM
  document.body.innerHTML = `
    <div id="test-element">Initial</div>
  `;

  // Test your function
  updateDOM("test-element", "Updated");

  // Assert changes
  expect(document.getElementById("test-element").textContent).toBe("Updated");
});
```

### Test Environment Selection

Jest automatically detects the appropriate environment:

- Files with DOM operations run in jsdom environment
- Pure JavaScript tests run in Node.js environment
- You can force an environment by adding `@jest-environment jsdom` or `@jest-environment node` at the top of test files

## Coverage Reports

Coverage reports show which parts of your code are tested:

- Open `coverage/lcov-report/index.html` in your browser for detailed HTML reports
- Coverage thresholds can be configured in `jest.config.js`

## Adding New Features

1. Create your module in `src/`
2. Write tests in `tests/` with `.test.js` extension
3. Use `.dom.test.js` suffix for DOM-related tests
4. Run `npm test` to verify everything works

```

---

## 12. Project structure overview

Your final project structure should look like this:

```

my-js-app/
├── src/
│ ├── sum.js
│ └── updateLabel.js
├── tests/
│ ├── sum.test.js
│ └── updateLabel.dom.test.js
├── coverage/ # Generated coverage reports
├── babel.config.js
├── jest.config.js
├── package.json
├── package-lock.json
└── README.md

```


```
