---
mode: agent
---

# Post-template customization prompt

This prompt is used after you've generated a new repository from the template
`https://github.com/ColmKenna/ckWebComponents.git` and cloned it locally. It
assumes you are in the repository root and ready to apply project-specific
customizations.

If you want me to apply changes automatically, provide either the local
repository path (so I can edit files directly) or a GitHub token with `repo`
scope so I can push changes via the API.

## 1) Confirm repository context

- Confirm the local repository root path (default: current working directory).
- Confirm whether the remote `origin` should be updated to
	`https://github.com/ColmKenna/<new-tag-name>-webcomponent.git`.

## 2) New component tag name

- Enter the new custom element tag name (kebab-case), e.g. `my-widget` (to
	replace the template `hello-world`).
- Confirm you want the replacement to run across source, tests, docs,
	stories, examples and styles.

## 3) Replace tag/class names and CSS variables

- Update component source files to use `<new-tag-name>` in
	`customElements.define(...)` and related class/identifiers.
- Rename selectors and classes in styles: `.hello-world` -> `.<new-tag-name>`
	(including BEM blocks/elements/modifiers).
- Rename CSS custom properties: `--hello-world-*` -> `--<new-tag-name>-*`.
- Rename style files and update imports if filenames include the old tag.
- Update all query selectors and test selectors that reference old class
	names/attributes.

## 4) package.json metadata

For each field below, ask whether to keep the template default or provide a
new value. If the user chooses new, accept the input and validate basic
formatting where appropriate.

- `name` — set to `@colmkenna/ck-webcomponents/<new-tag-name>`
- `description` — set to `A web component library featuring <new-tag-name>.`
- `version`
- `author`
- `license`
- `keywords` (comma-separated)
- `repository`
- `homepage`
- `bugs`

Keep npm scope as `@colmkenna` unless explicitly requested otherwise.

Also update `main`, `module`, `exports` and `files` entries to point to
`dist/<new-tag-name>.js` where applicable.

## 5) rollup.config.js (build)

- Replace `WebComponentLibrary` with `WebComponentLibrary/<new-tag-name>`.
- Ensure the build output filename is `dist/<new-tag-name>.js` (set
	`output.file` or `output.entryFileNames` as appropriate).
- Keep format and exports consistent with the template's expectations.

## 6) README.md and examples

- Replace `hello-world` references with `<new-tag-name>` in usage examples,
	script tags, and import paths.
- Update the project title, short summary, author info and demo link (if any).

## 7) Tests and stories

- Update unit and integration tests to reference the new tag and adjust
	imports/paths.
- Update Storybook stories / example pages to import and demonstrate the
	new component tag.

## 8) CI/CD and .github/workflows

- Update workflow names and any referenced paths or variables that still
	mention the old package name or build artifact.
- Ask the user whether they want automatic publishing via GitHub Actions (this
	requires a token/secret) or prefer manual publishing.

## 9) Git

- Ensure remote `origin` points to
	`https://github.com/ColmKenna/<new-tag-name>-webcomponent.git` (if
	applicable).
- Commit and push the customized scaffold. Suggested PowerShell commands:

```powershell
git checkout -b main 2>$null; git branch -M main
git add .
git commit -m "Initial scaffold from template"
git push -u origin main
```

## 10) Logging and manual steps

- Produce a changelog / log of all automated replacements and files edited.
- List any manual steps remaining (e.g. update repository settings, enable
	Actions, add secrets, configure npm publishing settings).