---
mode: agent
---
# Analyse Project Prompt

Analyze the entire codebase in this repository. Your output should consist of multiple markdown files, following these instructions. Place all generated markdown files in the repository root (or specify a `docs/` folder if preferred).

## Distinct Project Analysis Focus
For each distinct project or major module, perform a thorough analysis to determine:
- The project's specific purpose and the business/domain problem it addresses.
- The main entry points for the project (e.g., startup files, API endpoints, orchestrators).
- How users and developers interact with these entry points, and how code amendments propagate within the project.
- Provide actionable recommendations for amending code in the context of the project, including best practices for testing and validation.

## What is a "Distinct Project"?
A distinct project is any major module, service, or component that can be built, deployed, or maintained independently, typically with its own entry point, configuration, or runtime context.

## Example Output Files
- `README.md` (solution overview)
- `api_service.technical.readme.md`
- `frontend.technical.readme.md`
- `data_processor.technical.readme.md`

---

## 1. Individual Project technical.readme.md Files (named [project_name].technical.readme.md)

For each distinct project or significant module identified within the repository, generate a separate, comprehensive, and technically formal `technical.readme.md` file. Name each file using the convention `[project_name].technical.readme.md` (e.g., `user_service.technical.readme.md`, `data_processor.technical.readme.md`).

Each `technical.readme.md` file must be specifically tailored for a development team working on that particular project. It should aim to be a definitive guide for understanding, setting up, contributing to, and maintaining that specific project. Use consistent formatting, terminology, and structure across all generated markdown files. Each file must include:

- **Technical Project Overview:**  
  Structure this section as follows:

  - **Core Problem & Context**
    - Clearly state the specific pain points or challenges this project addresses.
    - Describe the context in which these problems arise within the overall system or business domain.
    - Articulate the project's purpose and how it fits into the larger solution.

  - **Entry Points & Code Amendment Guidance**
    - Identify the main entry points (e.g., main files, startup scripts, API endpoints) for this project.
    - Explain how to locate and modify code relevant to these entry points within the project.
    - Provide actionable recommendations for safely amending code, including best practices for testing and validation.

  - **Detailed Functionality & Capabilities**
    - Break down the primary function into granular capabilities.
    - List key features or services provided by this project.

  - **Key Technologies & Rationale**
    - For each major technology used, briefly justify its selection.
    - Consider factors such as performance, scalability, infrastructure, development speed, community support, architectural requirements, or integration needs.

  - **Role within the Overall Solution & System Interaction**
    - Articulate how this project fits into the end-to-end user journey or data flow.
    - Describe direct upstream and downstream dependencies.
    - Identify which other projects/services it depends on and which depend on it (i.e., are its consumers).


- **Detailed Installation and Setup Instructions:**  
  Provide step-by-step guidance for both runtime deployment and the local development environment for this specific project. This must cover prerequisites (e.g., specific language versions, databases, external services), environment variable configurations, and build procedures relevant to the project.

- **Usage Examples:**  
  Include practical usage examples and code snippets demonstrating key functionalities, common workflows, and typical configuration scenarios relevant to this project. Show how to interact with its main components or APIs.

- **API/Internal Module Documentation:**  
  If applicable to this project, generate comprehensive API documentation detailing endpoints, request/response formats (including examples), authentication mechanisms, and expected error codes. For internal APIs or module interfaces, document the primary classes/functions, their responsibilities, and key parameters within this project.

- **Contribution Guidelines:**  
  Outline clear and actionable contribution guidelines for this specific project, including preferred branching strategy, commit message conventions, code style enforcement (if any), testing procedures (how to run tests, add new ones), and the process for submitting pull requests or code changes.

- **Project Structure Breakdown:**  
  Provide a detailed breakdown of this project's directory and module structure, explaining the purpose and responsibilities of each major module, package, or directory. Illustrate how different parts of this project's codebase interact. Consider including diagrams for clarity.

- **Dependency Injection (DI) Details:**  
  Clearly identify and explain any Dependency Injection (DI) frameworks or containers utilized within this project, detailing their configuration, how dependencies are registered and resolved, and the rationale behind their use. Provide examples of dependency definitions.

- **Key Architectural Decisions:**  
  Briefly explain any significant architectural decisions or design patterns that govern this project's codebase structure and behavior.

- **Testing Strategy:**  
  Describe how tests are organized, run, and how new tests should be added for this project.

- **Troubleshooting/Common Issues:**  
  Include a section on frequently encountered problems and their solutions during development or deployment of this project. Where possible, provide links to external resources, diagnostic commands, or log file locations.

- **Maintaining Documentation:**  
  Add a section describing how and when to review and update documentation as the project evolves.

---

## 2. Overall Solution README.md (named README.md)

Once all individual project `technical.readme.md` files are generated, immediately proceed to generate a high-level `README.md` for the entire repository. This file should act as an entry point for understanding the complete solution. It must include:

- **Solution Overview:**  
  A concise overview of the entire repository's purpose, what overarching problem it solves, and its primary functionalities.

- **Identified Projects:**  
  A clear list of each distinct project or component identified within the repository. For each, provide a brief description of its role within the larger solution and reference its dedicated `technical.readme.md` file.

- **Solution Entry Points:**  
  Clearly identify and explain the primary entry points for the entire solution (e.g., main application startup, core API gateway, orchestrator services). Describe how to initiate or interact with the system as a whole.

- **Amending the Codebase:**  
  Provide general guidance on how to safely amend code across the solution, including best practices for identifying affected entry points, testing changes, and validating integration.

- **Overall Architecture Sketch (Conceptual):**  
  A high-level description or conceptual diagram (consider using Mermaid, PlantUML, or ASCII) of how the identified projects/components interact to form the complete solution.

Each `technical.readme.md` file must be specifically tailored for a development team working on that particular project. It should aim to be a definitive guide for understanding, setting up, contributing to, and maintaining that specific project. Use consistent formatting, terminology, and structure across all generated markdown files. Each file must include:

- **Technical Project Overview:**  
  Clearly state the project's core problem domain, its primary function, and the key technologies it leverages. Emphasize its role within the larger solution.

- **Detailed Installation and Setup Instructions:**  
  Provide step-by-step guidance for both runtime deployment and the local development environment for this specific project. This must cover prerequisites (e.g., specific language versions, databases, external services), environment variable configurations, and build procedures relevant to the project.

- **Usage Examples:**  
  Include practical usage examples and code snippets demonstrating key functionalities, common workflows, and typical configuration scenarios relevant to this project. Show how to interact with its main components or APIs.

- **API/Internal Module Documentation:**  
  If applicable to this project, generate comprehensive API documentation detailing endpoints, request/response formats (including examples), authentication mechanisms, and expected error codes. For internal APIs or module interfaces, document the primary classes/functions, their responsibilities, and key parameters within this project.

- **Contribution Guidelines:**  
  Outline clear and actionable contribution guidelines for this specific project, including preferred branching strategy, commit message conventions, code style enforcement (if any), testing procedures (how to run tests, add new ones), and the process for submitting pull requests or code changes.

- **Project Structure Breakdown:**  
  Provide a detailed breakdown of this project's directory and module structure, explaining the purpose and responsibilities of each major module, package, or directory. Illustrate how different parts of this project's codebase interact. Consider including diagrams for clarity.

- **Dependency Injection (DI) Details:**  
  Clearly identify and explain any Dependency Injection (DI) frameworks or containers utilized within this project, detailing their configuration, how dependencies are registered and resolved, and the rationale behind their use. Provide examples of dependency definitions.

- **Key Architectural Decisions:**  
  Briefly explain any significant architectural decisions or design patterns that govern this project's codebase structure and behavior.

- **Testing Strategy:**  
  Describe how tests are organized, run, and how new tests should be added for this project.

- **Troubleshooting/Common Issues:**  
  Include a section on frequently encountered problems and their solutions during development or deployment of this project. Where possible, provide links to external resources, diagnostic commands, or log file locations.

- **Maintaining Documentation:**  
  Add a section describing how and when to review and update documentation as the project evolves.

---

Please reason through the entire repository’s contents step-by-step to identify distinct projects, their entry points, and their characteristics before composing any of the README files to ensure accurate and complete documentation. Focus detailed analysis and amendment guidance on each distinct project, while keeping the overall README high-level.