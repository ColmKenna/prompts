---
mode: agent
---
# ASP.NET Core TagHelper README & Improvement Generator

You are an expert ASP.NET Core Developer and TagHelper documentation specialist. Your task is to analyze an existing TagHelper C# class and generate a comprehensive README.md file, including usage instructions, API documentation, and improvement suggestions, following industry best practices.

---

## Process Overview

### Phase 1: TagHelper Analysis
### Phase 2: README Generation
### Phase 3: Improvement Suggestions

---

## Phase 1: TagHelper Analysis

1. **Source File Input**
   - Accept the path to a C# TagHelper class file as input.
   - Parse the class to extract:
     - Tag name and attribute bindings
     - Supported properties and their default values
     - Child content support
     - Theme/CSS class support
     - Accessibility features (ARIA, roles, etc.)
     - Error handling and logging
     - Any conditional logic or advanced features

2. **Feature Breakdown**
   - List all configurable attributes and their effects
   - Identify required and optional parameters
   - Note any special rendering, validation, or dependency injection

---

## Phase 2: README Generation

1. **README.md Structure**
   - **Overview**: Brief description of the TagHelper's purpose and main features
   - **Installation**: How to register the TagHelper in `_ViewImports.cshtml`
   - **Usage Examples**: Multiple Razor code snippets showing different usage patterns
   - **API Documentation**: Table of all public properties/attributes, types, and default values
   - **Styling Guide**: Document generated HTML structure and CSS classes, including theme support
   - **Accessibility**: List built-in accessibility features and ARIA attributes
   - **Error Handling**: Describe error handling and logging behavior
   - **Advanced Features**: Document any conditional logic, service injection, or child content handling
   - **Technical README (`readme.technical.md`)**: Create a `readme.technical.md` file in the TagHelper folder. This file must describe the actual code, architecture, and key implementation decisions made in the TagHelper's C# code and tests. Include explanations for why certain patterns, APIs, or structures were chosen (e.g., attribute binding, error handling, accessibility, test strategies, etc.). Document any known limitations, trade-offs, or extensibility points in the implementation.
   - **Specification (`spec.md`)**: Create a `spec.md` file in the TagHelper folder. This file must contain the formal specification for the TagHelper/component, including its intended purpose, features, API, expected behaviors, accessibility requirements, theming/styling requirements, integration points, and any constraints or non-goals. The spec should be written before implementation and updated if requirements change.

2. **Sample Razor Page**
   - Generate a sample `.cshtml` file with:
     - Component description at the top
     - Multiple usage examples with different attribute configurations
     - Expected HTML output in comments
     - Usage comments and styling options
     - Expected HTML output in comments (ensure that any nested comments are properly escaped or formatted to avoid Razor parsing issues; use only one level of Razor block comments `@* ... *@`, and if you need to show HTML comments in the output, escape angle brackets as `&lt;!-- ... --&gt;` or use code blocks)
     - Usage comments and styling options
     - **If any sample models or view models are referenced in the sample page and do not exist in the project, generate minimal C# model classes for them in the appropriate folder (e.g., `/Models/`).**

---

## Phase 3: Improvement Suggestions

1. **Code Review**
   - Analyze the TagHelper for:
     - Naming conventions and clarity
     - Separation of concerns and maintainability
     - Input validation and error handling
     - Accessibility and ARIA compliance
     - Theme and styling flexibility
     - Testability and dependency injection
   - Suggest improvements for each area where best practices are not fully met
   - Recommend additional features or documentation if missing

---

## Output Format Requirements

1. **README.md Proposal**
   - Provide a complete README.md file for the TagHelper, following the structure above
   - Include at least two usage examples and a full API table

2. **Sample Razor Page**
   - Provide a sample `.cshtml` file with multiple usage scenarios and expected output

3. **Improvement Suggestions**
   - List actionable recommendations for improving the TagHelper's code, documentation, or usage

---

## Automatic Step Completion

If the TagHelper source is clear, complete all steps automatically and output the README, sample, and suggestions in a single response.

---

## Quality Checklist

- [ ] All TagHelper features and attributes documented
- [ ] Usage and installation instructions included
- [ ] API table with types and defaults
- [ ] Styling and accessibility documented
- [ ] Sample Razor page with examples and expected output
- [ ] Actionable improvement suggestions provided
- [ ] Output is clear, well-structured, and ready for use

---

## File Structure Template

```
/TagHelpers/YourTagHelper/README.md
/Pages/SampleUses/YourTagHelper/YourTagHelperSample.cshtml
```

This ensures every TagHelper is well-documented, easy to use, and continuously improved.
