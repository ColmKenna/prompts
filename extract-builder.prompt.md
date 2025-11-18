---
mode: edit
---

### Goal
Generate a **Builder** design pattern implementation for a given class, promoting immutability and a fluent API.

### Context
-   **Input Class**: The user will provide a class definition.
-   **Language**: The programming language should be inferred from the syntax of the input class.

### Core Requirements
1.  **Builder Class**:
    -   Generate a new class for the Builder, named `[OriginalClassName]Builder`.
    -   The Builder should be a **static inner class** if the language supports it (e.g., Java, C#). Otherwise, it should be a separate top-level class.

2.  **Fluent Interface**:
    -   All setter methods in the builder (`with[FieldName]`) must return the builder instance (`this`) to enable **method chaining**.

3.  **Field Support**:
    -   The Builder must support **all fields** present in the target class.
    -   Infer which fields are **required** vs. **optional**. A good heuristic is:
        -   **Required**: Fields in the constructor, non-nullable fields, or fields marked with annotations like `@NonNull`.
        -   **Optional**: Fields that can be set via setters or are nullable.

4.  **Collection Handling**:
    -   For collection types (e.g., `List`, `Set`, `Map`), generate two types of methods:
        -   A method to set the entire collection (e.g., `withItems(List<Item> items)`).
        -   Singular methods to add one item at a time (e.g., `addItem(Item item)`).

5.  **Build Method**:
    -   The `build()` method should construct and return an instance of the target class.
    -   It must perform **validation** before object creation, ensuring all **required** fields have been set. Throw an `IllegalStateException` or equivalent error if validation fails.

6.  **Immutability (Target Class Modifications)**:
    -   The generated output should include the necessary modifications to the target class to make it immutable:
        -   Make the target class's constructor `private`. The builder will use this constructor.
        -   Ensure all fields in the target class are `final` or `readonly`.
        -   Remove all public setters from the target class.

7.  **Instance-to-Builder**:
    -   Provide a mechanism to create a builder from an existing instance of the target class. This is useful for creating modified copies.
    -   This can be a static factory method on the builder `public static [OriginalClassName]Builder from([OriginalClassName] instance)` or a `toBuilder()` method on the target class.

### Output Format
-   **Code Only**: The output must be only the generated code (the Builder class and the modified target class).
-   **No Explanations**: Do not include any extra text, comments, or explanations outside of the code itself (language-appropriate documentation comments are acceptable, but "Here is the code:" is not).
-   **Ready to Use**: The code must be syntactically correct and ready to be integrated.
