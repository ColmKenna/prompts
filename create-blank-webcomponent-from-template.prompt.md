---
mode: agent
---
Use GitHub’s Template Repository feature to generate a new repository from:
https://github.com/ColmKenna/ckWebComponents.git

Before proceeding, ask me:
- The new repository name (default: <new-tag-name>-webcomponent)
- Visibility (public/private)
- Whether to create via GitHub UI (“Use this template”) or via API
  - If via API, prompt for a GitHub token with `repo` scope and use `/repos/ColmKenna/ckWebComponents/generate`.

After generating the new repository and cloning it locally, interactively prompt me for the following:

1. **New Component Tag Name**
   - Ask me for a new tag name to replace the default <hello-world>.
  - Work in the repository root (no nested folder).
      - Apply this tag across the project:
        - Update component source files.
        - Update all CSS/SCSS modules and styles:
          - Rename any component-specific class/selector names to match <new-tag-name> (e.g., `.hello-world` -> `.<new-tag-name>`; BEM blocks/elements/modifiers included).
          - Update custom CSS variable prefixes or tokens derived from the component name (e.g., `--hello-world-*` -> `--<new-tag-name>-*`).
          - Adjust style imports and file names if they include the old tag name.
        - Update all references to these classes/selectors:
          - HTML/TS/JS templates, tests, stories, docs, and examples.
          - Any query selectors (e.g., `querySelector`, `getByRole`, `getByClass`), and DOM-manipulation code using old class names.
        - Update all test files to reference <new-tag-name> and adjust imports/paths to the new component as needed.
        - In package.json:
          - Set "name": "@colmkenna/ck-webcomponents/<new-tag-name>"
          - Set "description": "A web component library featuring <new-tag-name>."
       - In rollup.config.js:
         - Replace "WebComponentLibrary" with "WebComponentLibrary/<new-tag-name>".
         - Set the build output filename to `dist/<new-tag-name>.js` (via `output.file` or `output.entryFileNames`).
         - Ensure the format and exports remain consistent with the template.
       - In package.json:
         - Update `main`/`module`/`exports.browser` (as applicable) to point to `dist/<new-tag-name>.js`.
         - Update any `files` array entries if they reference the old build name.
       - In README.md and examples:
         - Update script tags and import paths to reference `dist/<new-tag-name>.js`.
       - In README.md:
         - Replace references to "hello-world" with <new-tag-name>.
         - Update usage examples.
      - Use "https://github.com/ColmKenna/<new-tag-name>-webcomponent.git" as the default GitHub repository URL.

2. **package.json Metadata**
   - For each field, ask if I want to **use the default value** from the template or **enter a new one**:
     - version
     - author
    ````prompt
    ---
    mode: agent
    ---

    # Create a new web component from the ckWebComponents template

    Use GitHub’s Template Repository feature to generate a new repository from:
    https://github.com/ColmKenna/ckWebComponents.git

    Before creating the repo, ask the user:

    - New repository name (default: `<new-tag-name>-webcomponent`)
    - Visibility (public / private)
    - Creation method: GitHub UI ("Use this template") or API
      - If via API: request a GitHub token with `repo` scope and call `/repos/ColmKenna/ckWebComponents/generate`.

    After generating the repository and cloning it locally, run the interactive customization below.

    ## Post-clone prompts and replacements

    These steps should be executed in the repository root (no nested folder). Replace every occurrence of the template's default tag (e.g. `hello-world`) with the new component tag `<new-tag-name>`.

    ### 1) New component tag name

    - Ask for the new custom element tag (kebab-case), e.g. `my-widget`.
    - Confirm you want to replace `hello-world` with `<new-tag-name>` throughout the project.

    ### 2) Source code and templates

    - Update `customElements.define(...)` and the component class name to use the new tag where applicable.
    - Update any inline templates, HTML examples, and example pages to use `<new-tag-name>`.

    ### 3) Styles (CSS / SCSS)

    - Rename selectors and classes: `.hello-world` -> `.<new-tag-name>` (including BEM blocks/elements/modifiers).
    - Rename custom properties: `--hello-world-*` -> `--<new-tag-name>-*`.
    - Rename style files and update imports if filenames include the old tag.

    ### 4) Query selectors and DOM tests

    - Update all selectors in code/tests (e.g. `querySelector`, test queries such as `getByRole`, `getByText`, `getByTestId`) that reference the old tag or class names.

    ### 5) package.json

    - Set `name` to `@colmkenna/ck-webcomponents/<new-tag-name>`.
    - Set `description` to `A web component library featuring <new-tag-name>.`.
    - For each of the following fields ask whether to keep the template default or provide a new value: `version`, `author`, `license`, `keywords`, `repository`, `homepage`, `bugs`.
    - Keep the npm scope `@colmkenna` unless the user asks to change it.
    - Update `main` / `module` / `exports` / `exports.browser` (as applicable) and any `files` entries to point to `dist/<new-tag-name>.js`.

    ### 6) rollup.config.js (build)

    - Replace the global name `WebComponentLibrary` with `WebComponentLibrary/<new-tag-name>`.
    - Ensure the output filename is `dist/<new-tag-name>.js` (use `output.file` or `output.entryFileNames`).
    - Keep format and exports consistent with the template.

    ### 7) README.md and examples

    - Replace `hello-world` references with `<new-tag-name>`.
    - Update usage examples, script tags, and import paths to reference `dist/<new-tag-name>.js`.
    - Update project title, summary, author info, and demo link (if any).

    ### 8) Tests and stories

    - Update unit tests to assert against `<new-tag-name>` and adjust imports/paths.
    - Update Storybook stories or examples to import and render `<new-tag-name>`.

    ### 9) CI/CD (.github/workflows)

    - Update workflow names and any referenced paths / package names to match the new project and build artifact.
    - Ask the user whether they want automatic publishing via GitHub Actions (this requires a token and secrets).

    ### 10) Git configuration and push

    - Update remote `origin` to: `https://github.com/ColmKenna/<new-tag-name>-webcomponent.git` (if applicable).
    - Commit and push the customized scaffold. Suggested PowerShell commands:

    ```powershell
    git checkout -b main 2>$null; git branch -M main
    git add .
    git commit -m "Initial scaffold from template"
    git push -u origin main
    ```

    ## Final output

    - Produce a log of all files changed and replacements performed.
    - List any manual steps that remain (e.g. enable Actions, add repository secrets, update repository settings, publish to npm).

    If you want me to execute the replacements automatically, provide either:

    - The local repository path (so I can edit files directly), or
    - A GitHub token with `repo` scope to make changes via the API and push the branch.

    ````
