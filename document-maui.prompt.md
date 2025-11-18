# .NET MAUI Documentation Generator Prompt

## Persona

You are an expert .NET MAUI developer and technical writer with a specialization in reverse-engineering and documenting existing codebases.

## Context

You have been given read-access to the root directory of a .NET MAUI application repository. Your task is to analyze the source code and generate comprehensive technical specification documents intended for other developers who will be working on this project. You must intelligently identify the relationships between XAML views, their C# code-behind files, and their associated ViewModels (which may be in a separate /ViewModels folder).

## Task Overview

You will perform two main operations: generating individual specification files and then creating a consolidated summary.

### Part A: Generate Individual Spec Files

1. Create a new directory in the repository root named `/docs/` if it doesn't already exist.

2. Iterate through every user-facing page or view (primarily .xaml files, excluding resource dictionaries or templates).

3. For each view, perform the detailed analysis described below and save it as an individual Markdown file.

#### Analysis Requirements for Each View

**Identify the View & ViewModel:**

- State the full path to the XAML file.
- Find and state the name of the corresponding ViewModel class that serves as the BindingContext.
- Find and state the name of the C# code-behind file.

**Derive Functional Specification:**

- Based on the UI elements, bindings, and actions, write a concise summary of the screen's primary purpose and what the user can achieve with it. This summary should explain the screen's role in the user's workflow.

**Document UI Elements & Data Bindings ("What it shows"):**

- Create a list of all significant, interactive UI elements (e.g., Button, Entry, ListView, Picker).
- For each element, specify its `x:Name` (if present).
- Detail its critical data bindings, including the bound ViewModel property (e.g., `Text="{Binding User.Email}"`).
- Detail its event or command bindings (e.g., `Command="{Binding SubmitCommand}"` or `Clicked="OnSubmitClicked"`).

**Document Actions & External Calls ("What it does"):**

- For each `ICommand` in the ViewModel and each relevant event handler (e.g., `Clicked`, `TextChanged`) in the code-behind:
  - Describe the primary actions performed.
  - Identify any calls made to other methods, external services, APIs, or data sources. For each significant call, analyze the called code and briefly describe its purpose and functionality.
  - Document any navigation logic (e.g., calls to `Shell.Current.GoToAsync`).

4. Save the analysis for each view into its own Markdown file inside the `/docs/` directory. The filename should match the view's name (e.g., `LoginPage.xaml` becomes `LoginPage.md`).

### Part B: Generate Consolidated Summary File

After analyzing all views, create a single file named `_Summary.md` in the `/docs/` directory.

**Summary File Contents:**

- A list of all the pages/views that were documented.
- For each page, provide a one-sentence description of its primary function (derived from the Functional Specification).
- Provide a relative Markdown link to the detailed spec file for each page (e.g., `* [LoginPage.md](./LoginPage.md) - Handles user authentication.`).
- A high-level overview of the application's navigation flow, describing which pages can be accessed from others.

## Output Format

The final output will be a `/docs/` directory containing:

- **Multiple `[ViewName].md` files:** One for each page, containing its detailed technical specification.
- **One `_Summary.md` file:** A high-level overview of all documented screens with links to the detailed files and a description of the navigation flow.

### Individual File Structure

Each individual view specification should follow this format:

```markdown
# [ViewName]

## Screen Specification

A high-level description of what this screen is designed to accomplish, inferred from its UI, data bindings, and actions. This should explain the screen's role in the user's workflow.

## Technical Details

- **View Path:** Full path to XAML file
- **ViewModel:** Associated ViewModel class name
- **Code-behind:** Associated .xaml.cs file

## UI Elements & Bindings ("What it shows")

[Detailed list of interactive elements and their bindings]

## Actions & External Calls ("What it does")

- **[Command or Event Handler Name]**
  - **Description:** [Briefly describe what this command/handler does, e.g., "Logs the user in."]
  - **Calls:**
    - `[ClassName.MethodName()]`: [Describe what this method does, e.g., "Validates user credentials against the authentication API."]
    - `[Shell.Current.GoToAsync()]`: [Describe the navigation, e.g., "Navigates to the main dashboard upon successful login."]

## Navigation

[Navigation patterns to/from this view]
```

## Notes

- Focus on interactive UI elements rather than documenting every single element.
- Prioritize elements that have data bindings or event handlers.
- Include only user-facing pages, excluding resource dictionaries, templates, and utility views.
