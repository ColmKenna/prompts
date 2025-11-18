---
mode: agent
---
# Prompt for Generating Requirements Documents

## 🎯 Purpose
Generate a **Requirements Document** from provided code files by analyzing only the **public API** (exported functions, classes, events, and documented members). Produce **user stories** with clear **acceptance criteria** that explicitly include **nominal, error, and edge case scenarios**, plus structured **nonfunctional requirements (NFRs)**.

---

## 🔧 Scope & Constraints
- **Focus depth:** Public API + observable behaviors only  
- **Exclude:** Private/internal helpers, implementation details, TODOs, comments suggesting future features  
- **Testing levels implied:** Unit and Integration (label each criterion `[Unit]` or `[Integration]`)  
- **Nonfunctional items:** Include when inferable (Performance, Security, Reliability, Compatibility, Accessibility, Observability)  
- **Bounded contexts:** If multiple modules/files, treat each as its own bounded context. Keep user stories scoped accordingly.  

---

## 📐 Output Format

```
# Requirements Document

## Introduction
One short paragraph describing the module/component’s public purpose, primary capabilities, inputs/outputs, and observable side effects.

## Requirements

### Requirement 1
**User Story:** As a {{persona}}, I want {{capability}} so that {{benefit}}.

#### Acceptance Criteria (Nominal, Error, Edge)
1. [Unit] GIVEN the public API function `{{functionName}}` WHEN called with valid input `{{input}}` THEN the system SHALL return `{{expectedOutput}}` (Nominal Case)
2. [Unit] GIVEN the public API function `{{functionName}}` WHEN called with invalid input `{{null/undefined/wrongType}}` THEN the system SHALL throw `{{ErrorType}}` (Error Case)
3. [Integration] GIVEN the public API function `{{functionName}}` WHEN called with edge case input `{{boundary/largeInput}}` THEN the system SHALL {{observable outcome, e.g., performance guarantee or specific behavior}} (Edge Case)

### Requirement 2
**User Story:** ...
#### Acceptance Criteria (Nominal, Error, Edge)
1. [Unit] GIVEN the public API method `{{methodName}}` WHEN called under normal conditions THEN the system SHALL {{expected behavior}} (Nominal Case)
2. [Unit] GIVEN the public API method `{{methodName}}` WHEN called with missing/invalid parameters THEN the system SHALL throw `{{ErrorType}}` (Error Case)
3. [Integration] GIVEN the public API method `{{methodName}}` WHEN executed in extreme conditions (e.g., high concurrency, large input) THEN the system SHALL {{outcome}} (Edge Case)
```

---

## 🛠 Nonfunctional Requirements (NFRs)
- **Performance:** GIVEN the public API `{{functionName}}` WHEN invoked 1000 times concurrently THEN the system SHALL respond within {{time constraint}}  
- **Accessibility (if UI):** GIVEN the public API `{{componentName}}` WHEN rendered THEN the system SHALL expose `{{role/label/focus behavior}}`  
- **Security:** GIVEN the public API `{{functionName}}` WHEN receiving untrusted input THEN the system SHALL sanitize it and prevent injection  
- **Reliability:** GIVEN the public API `{{functionName}}` WHEN a timeout or network error occurs THEN the system SHALL retry {{N}} times before failing gracefully  
- **Compatibility:** GIVEN the public API `{{functionName}}` WHEN executed in {{environment/version}} THEN the system SHALL provide a fallback or clear error message  
- **Observability:** GIVEN the public API `{{functionName}}` WHEN execution fails THEN the system SHALL log `{{logLevel}}` with `{{details}}`  

---

## 📖 Assumptions & Open Questions
- Assumption: {{list inferred assumptions about visibility, inputs, or outputs}}  
- Question: {{list unclear API contracts, undocumented payloads, or ambiguous behaviors}}  

---

## ✅ Style & Quality Rules
- Each requirement MUST be: Independent, Negotiable, Valuable, Estimable, Small, Testable (INVEST).  
- Each acceptance criterion MUST reference a **public API function, method, event, or class** explicitly in the **GIVEN** part.  
- Every requirement MUST include at least one **Nominal Case**, one **Error Case**, and one **Edge Case**.  
- Phrase criteria in **GIVEN / WHEN / THEN** format.  
- Ensure **traceability**: always link criteria back to the exact public API name.  
- Use **personas**: Developer, Tester, Operator, End-user.  
- If API workflows exist, describe them as **Domain Stories** (Actor → Action → Work Object).  

---

## ✨ Prompting Techniques Integrated
- **Tree-of-Thought**: Explore alternative input/output paths and edge cases.  
- **Self-Consistency**: Generate multiple candidate acceptance criteria, refine to the most plausible/testable.  
- **Iterative Refinement**: Allow expansion on specific requirements (e.g., “expand Requirement 3 with additional error cases”).  

---

## 📥 Input
Paste code files or reference them here:  

```
// Example: public API from myComponent.js
export function calculateTotal(values) { ... }
export class MyComponent { 
  public toggleState(flag) { ... }
  public onEvent(callback) { ... }
}
```
