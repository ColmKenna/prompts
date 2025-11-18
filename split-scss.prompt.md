# Split SCSS into modular partials

Purpose
- Given a single .scss file used within a .NET Core project (assumed small-to-medium), produce a modular refactoring plan and implement it.
- Break the original SCSS into reusable, maintainable partials following SCSS best practices.

Scope & Constraints
- Maintain all existing comments and groupings for context.
- Avoid duplicating styles; promote separation of concerns.
- Result must be suitable for drop-in replacement in the project.
- Use the modern Sass module system: replace deprecated `@import` with `@use` / `@forward`. Provide guidance on namespacing (or `as *` where appropriate) and ordering so variables/mixins are loaded before components.
- Update build instructions to compile a single entry (e.g., `scss/main.scss`) directly; include or update a small README or build script in deliverables to document the exact CLI command.
- Include source-map guidance: recommend `--embed-source-map` or `--source-map` during development and `--no-source-map` (or CI/production-appropriate flags) for production builds; include example Sass CLI flags.

Required deliverables
1. Step 1 — Planning
   - Proposed partial filenames (use `_filename.scss` convention) and folder paths.
   - A short description of each file's purpose.
   - Which parts of the original SCSS should move to each partial (based on selectors, comments, or semantic groups).
   - Notes to ensure no style duplication and clear separation of concerns.

2. Step 2 — Implementation
   - Rewrite the original SCSS into modular partials according to the plan.
   - Replace any `@import` usage with `@use` / `@forward`, and provide a brief note explaining namespaces used (or `as *` exceptions).
   - Include a main `index.scss` (or `main.scss`) that uses the module system in the correct order (variables/mixins first, then base/global, then components, then utilities).
   - Keep all original comments and groupings for context.
   - Provide a small README or updated build script snippet that compiles `scss/main.scss` to the project CSS output and documents the chosen source-map flags and production settings.

3. Output format
   - A tree view of the new SCSS folder structure.
   - Code blocks for the contents of each new SCSS file (including the main index).
   - Each code block must start with a single-line comment containing the filepath.
   - Include a README or build-script snippet showing the exact `sass` CLI commands (dev and prod examples) and source-map flags.
   - The output should be a ready-to-use, drop-in replacement and reflect SCSS best practices.

Example (format guidance)
- Use the `_filename.scss` convention for partials.
- Provide a concise mapping from original selectors/comments to new partials.
- Example snippet format:
  - Tree:
    ```
    scss/
    ├─ _variables.scss
    ├─ _mixins.scss
    ├─ base/
    │  └─ _base.scss
    ├─ components/
    │  ├─ _button.scss
    │  └─ _card.scss
    └─ main.scss
    ```
  - Example `main.scss` using the module system:
    ```
    <!-- filepath: scss/main.scss -->
    // Load variables & mixins first (namespaced or as *)
    @use 'variables' as *;
    @use 'mixins';

    // Load base and components (components should @use or rely on forwarded modules)
    @use 'base/base';
    @use 'components/button';
    @use 'components/card';

    // ...existing code...
    ```
  - README / build script snippet (example commands):
    ```
    <!-- filepath: scss/README.md -->
    # Build CSS
    # Development (embed source maps)
    sass --embed-source-map scss/main.scss wwwroot/css/main.css --style=expanded

    # Production (no source maps, compressed)
    sass --no-source-map scss/main.scss wwwroot/css/main.css --style=compressed
    ```

Notes
- Aim for modularity: variables, mixins, globals, components, utilities.
- Import/module order matters: variables and mixins first, then base/global, then components, then utilities.
- Prefer `@use` / `@forward` to eliminate deprecation warnings; document any `as *` usage in comments.
- Document the exact Sass CLI options used for development and production (source maps, style) in the provided README or build scripts.