---
mode: agent
---
Function Analysis & Behavior Summary (Language-Aware)

**Goal:** From the files provided in context, analyze **every function** across all files and generate a **single Markdown report** that summarizes behavior, documents inputs/outputs/side effects, and enumerates any errors or edge cases in line with idiomatic expectations for each detected language.

**Context you have:**

* One or more **code files** may be present. Inspect each file separately and include every function discovered.
* **Determine language conventions** per file before analyzing:
  * **JavaScript / TypeScript:** Follow Airbnb JavaScript/TypeScript guidelines (camelCase functions, explicit returns, minimal side effects, precise typing/JSDoc).
  * **C# / .NET:** Follow .NET naming and design guidelines (PascalCase methods, meaningful identifiers, explicit types, guard clauses, clear error handling).
  * **Other languages:** Apply the same clarity, purity, and explicitness principles. Identify any language-specific gaps so the recipient understands where guidance may need adjustment.
* If multiple languages appear, call that out explicitly and apply guidance per language.
* **Inference rule:** If a fact is obvious from the file, use it. If not, preface the statement with `Assumption:` and explain briefly.

**Tasks:**

1. **Enumerate functions**

   * Identify all top-level functions, class methods, and nested/inner functions in every file.
   * Use fully qualified names (for example `fileScope.func`, `Module.Class.method`, `Namespace.func`). Prefix with the file path or module when needed to avoid collisions (for example ``fileA.ts.Module.func``).

2. **Analyze each function (concise, high-signal):**

   * **Summary (<=3 sentences):** Purpose, key side effects, notable constraints.
   * **Signature & Types:** Current signature; infer missing/implicit types (mark assumptions).
   * **Inputs / Outputs / Side effects:** Parameters, return values, I/O, mutations (globals, captured state), external calls.
   * **Errors & Edge Cases:** Known or implied error handling, thrown exceptions, guard clauses, boundary conditions, and unhandled scenarios.
   * **Complexity & Risks:** Deep branching, long parameter list, hidden state, tight coupling, error handling gaps.
   * **Suggested renames:** Provide **two or three** language-appropriate alternatives, each with a one-line rationale.

3. **Output (one Markdown file only):**

   * Begin with an **Index** listing every function (linked anchors) with a one-line purpose. Group or sort entries by file if multiple files are present.
   * Then create one section per function using **exactly** the schema below. Ensure anchors remain unique by including file prefixes when necessary.

````markdown
# Function Inventory & Behavior Summary (Language-Aware)

## Index
- [`scope.funcName`](#scopefuncname) - brief purpose
...

---

## scope.funcName
**Current name:** `funcName`  
**Suggested renames:**
- `betterFuncName` - rationale (language-aligned naming clarity)
- `alternateName` - rationale
- `thirdOption` - rationale (omit if only two options fit)

**Summary (<=3 sentences):**  
- What it does, key side effects, constraints.

**Signature & Types:**  
```language
function funcName(paramA: Type, paramB?: Type): ReturnType
```

**Inputs / Outputs / Side Effects:**

* Inputs: ...
* Returns: ...
* Side effects: filesystem/network/logging/global state/captured closure/etc.

**Errors & Edge Cases:**

* Known errors or exceptions...
* Edge case behavior...

**Complexity & Risks:**

* e.g., deep nesting, long params, hidden mutations, weak error handling.

**Dependencies:**

* Internal: `module.helperA`, `Class.methodB`
* External: `lib.method`, remote services, environment variables

**Notes:**

* Assumption: (only if needed)

```

**Method & guardrails:**
- Produce analysis only; **do not modify code**.
- Prefer concise rationales over step-by-step reasoning.
- Highlight assumptions or ambiguities so follow-up questions are easy to surface.
- Keep terminology descriptive and idiomatic for the detected language; add file qualifiers when needed for clarity.
- Rename suggestions should offer two or three actionable options that respect the conventions of the detected language.

**Deliverable:**
- Create a single file named **`FUNCTION_SUMMARY.md`** following the schema above and covering every function from all provided files.

---
