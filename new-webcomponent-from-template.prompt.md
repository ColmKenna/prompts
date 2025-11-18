---
mode: agent
---
# create From Template (Agent-Driven, Parametric)

Automation-friendly guide to create a new repository from the upstream template repository and then rename the bundled component to a user-provided name at run time.

Important: Do not start from an empty repo or from this repo directly. The process always begins by creating a new repository from the template using GitHub’s template feature.

Template to use (explicit): `https://github.com/ColmKenna/ckWebComponents.git`.
Current repo remote (for owner inference): see `git remote -v`.

## Prerequisites

- Git installed and configured.
- Node.js 18+ and npm.
- GitHub account with permission to use the template and create repositories.
- GitHub CLI (`gh`) installed and authenticated; this guide uses `gh` to create the repo from the template.

## Detected Changes Since Template (Categorized)

- Repository configuration and metadata
  - `package.json`: rename package to `@<scope>/<component-slug>`; update description; update repository/bugs/homepage URLs to point at the new repo.
- Code structure and files added/removed
  - Replace `hello-world` component with a user-provided component slug (kebab-case).
  - Add/rename: `src/components/<slug>/<dot-name>.ts`, `src/components/<slug>/<dot-name>.styles.ts`.
  - Update `src/index.ts` exports/imports to the new class name (PascalCase).
- CI/CD workflows
  - `.github/workflows/ci.yml` and `.github/workflows/deploy.yml` added in template and unchanged thereafter.
- Documentation and metadata
  - `README.md`: replace usage, tag, class, and dist paths from `hello-world` → `<slug>`.
- Dependencies and configuration files
  - `rollup.config.js`: change output file names and UMD global `name` to include `<slug>`.
  - `package.json`: change `main`/`module` outputs to `dist/<slug>/*` and fix package name format.
- Build artifacts
  - `dist/<slug>/*` are produced after build; legacy `dist/components/hello-world/*.d.ts` removed/migrated.

## Step‑by‑Step: Create and Customize a New Repo

1. Create a new repository from the template (required)
   - Replace `my-new-project` with your desired repository name.
   - Example (as requested): `gh repo create my-new-project --template https://github.com/ColmKenna/ckWebComponents.git`
   ```bash
   # Ensure you are authenticated
   gh auth status >/dev/null 2>&1 || gh auth login -w

   # Define the target repository name (new project)
   export REPO_NAME="my-new-project"

   # Create from this template repository
   gh repo create "${REPO_NAME}" \
     --template "https://github.com/ColmKenna/ckWebComponents.git" \
     --public

   # Clone and install
   git clone "https://github.com/$(gh api user -q .login)/${REPO_NAME}.git"
   cd "${REPO_NAME}"
   npm install
   ```

   Note: Do not run `git init` or create files manually. All files come from the template.

2. Define component inputs and derive names (agent-friendly)
   - Provide a kebab-case component slug, e.g., `my-awesome-widget`.
   - Run this step inside the repository you cloned in Step 1.
   - The script derives: PascalCase class, dot-form filename, npm scope, and owner from the cloned repo’s remote.
   ```bash
   # REQUIRED input from user
   export COMPONENT_SLUG="my-awesome-widget"   # kebab-case web component tag

   # Derived names
   export COMPONENT_DOT="$(printf "%s" "$COMPONENT_SLUG" | tr '-' '.')"               # my.awesome.widget
   export COMPONENT_CLASS="$(node -e "const s=process.argv[1];console.log(s.split('-').map(x=>x[0].toUpperCase()+x.slice(1)).join(''))" "$COMPONENT_SLUG")"  # MyAwesomeWidget
   export COMPONENT_CAMEL="$(node -e "const s=process.argv[1];let p=s.split('-').map(x=>x[0].toUpperCase()+x.slice(1)).join('');console.log(p[0].toLowerCase()+p.slice(1))" "$COMPONENT_SLUG")"    # myAwesomeWidget

   # Infer GitHub owner from current remote
   export ORIGIN_URL="$(git remote get-url origin 2>/dev/null || true)"
   export GITHUB_OWNER="$(node -e "const u=process.argv[1]||'';const m=u.match(/[/:]([^/]+)\/[\w.-]+(\.git)?$/);console.log(m?m[1]:'')" "$ORIGIN_URL")"

   # Infer npm scope from existing package.json name (left of slash)
   export NPM_SCOPE="$(node -e "const fs=require('fs');try{const n=JSON.parse(fs.readFileSync('package.json','utf8')).name||'';const m=n.match(/^@([^/]+)/);console.log(m?m[1]:'');}catch{console.log('');}" )"

   # If owner not derivable from current repo, fall back to gh-authenticated user
   if [ -z "$GITHUB_OWNER" ]; then
     export GITHUB_OWNER="$(gh api user -q .login 2>/dev/null || true)"
   fi
   ```

3. Rename the component folder and files (hello-world → ${COMPONENT_SLUG})
   ```bash
   # Rename files inside the folder, then rename the folder (preserves history)
   git mv src/components/hello-world/hello.world.ts        "src/components/hello-world/${COMPONENT_DOT}.ts"
   git mv src/components/hello-world/hello.world.styles.ts "src/components/hello-world/${COMPONENT_DOT}.styles.ts"
   git mv src/components/hello-world "src/components/${COMPONENT_SLUG}"
   ```

4. Update source exports/imports, class names, and selectors
   - Programmatically replace identifiers and tags across sources and tests:
     ```bash
     node <<'NODE'
     const fs=require('fs'),path=require('path');
     const slug=process.env.COMPONENT_SLUG;
     const dot=(slug||'').replace(/-/g,'.');
     const pascal=(slug||'').split('-').map(s=>s[0].toUpperCase()+s.slice(1)).join('');
     const camel=pascal?pascal[0].toLowerCase()+pascal.slice(1):'';
     const roots=['src','tests','README.md','rollup.config.js'];
     const files=[];
     function walk(d){for(const e of fs.readdirSync(d,{withFileTypes:true})){if(e.name.startsWith('.git'))continue;const p=path.join(d,e.name);e.isDirectory()?walk(p):files.push(p);}}
     for(const r of roots){ if(fs.existsSync(r)){ const st=fs.statSync(r); st.isDirectory()?walk(r):files.push(r);} }
     for(const f of files){ let s=fs.readFileSync(f,'utf8');
       s=s.replace(/hello-world/g, slug)
          .replace(/hello\.world/g, dot)
          // Also replace the current template's identifiers
          .replace(/ck-array-editor/g, slug)
          .replace(/ck\.array\.editor/g, dot)
          .replace(/\bHelloWorld\b/g, pascal)
          .replace(/\bhelloWorld\b/g, camel)
          .replace(/\bCkArrayEditor\b/g, pascal)
          .replace(/\bckArrayEditor\b/g, camel);
       fs.writeFileSync(f,s); }
     NODE
     ```

5. Update tests to the new tag and class
   ```bash
   mkdir -p "tests/${COMPONENT_SLUG}" 2>/dev/null || true
   if [ -f tests/hello-world/hello-world.test.ts ]; then
     git mv tests/hello-world/hello-world.test.ts "tests/${COMPONENT_SLUG}/${COMPONENT_SLUG}.test.ts"
   fi
   if [ -f tests/ck-array-editor/ck-array-editor.test.ts ]; then
     git mv tests/ck-array-editor/ck-array-editor.test.ts "tests/${COMPONENT_SLUG}/${COMPONENT_SLUG}.test.ts"
   fi
   ```
   - Ensure the test imports the new component path and class name.

6. Adjust build outputs
   - Ensure `rollup.config.js` outputs and UMD global name include the new slug:
     ```bash
     node <<'NODE'
     const fs=require('fs');
     const slug=process.env.COMPONENT_SLUG;
     const f='rollup.config.js';
     if(fs.existsSync(f)){
       let s=fs.readFileSync(f,'utf8');
       s=s
        // Support either starting point
        .replace(/dist\/(hello-world)\/(hello-world)/g, `dist/${slug}/${slug}`)
        .replace(/dist\/(ck-array-editor)\/(ck-array-editor)/g, `dist/${slug}/${slug}`)
        .replace(/name:\s*['"]WebComponentLibrary[^'"]*['"]/g, `name: 'WebComponentLibrary/${slug}'`);
       fs.writeFileSync(f,s);
     }
     NODE
     ```

7. Update package metadata and entry points
   ```bash
   # Derive scope and owner if missing
   export NPM_SCOPE="${NPM_SCOPE:-${GITHUB_OWNER}}"

   # Package identity and entry points
   npm pkg set name="@${NPM_SCOPE}/${COMPONENT_SLUG}"
   npm pkg set description="A web component library featuring ${COMPONENT_SLUG}."
   npm pkg set main="dist/${COMPONENT_SLUG}/${COMPONENT_SLUG}.js"
   npm pkg set module="dist/${COMPONENT_SLUG}/${COMPONENT_SLUG}.esm.js"

   # Repository links (derived from the current origin remote)
   npm pkg set repository.type='git'
   npm pkg set repository.url="https://github.com/${GITHUB_OWNER}/${COMPONENT_SLUG}.git"
   npm pkg set bugs.url="https://github.com/${GITHUB_OWNER}/${COMPONENT_SLUG}/issues"
   npm pkg set homepage="https://github.com/${GITHUB_OWNER}/${COMPONENT_SLUG}#readme"
   ```

8. Refresh README usage to the new tag/class/paths
   - Replace occurrences of:
     - `hello-world` and `ck-array-editor` → `${COMPONENT_SLUG}`
     - `HelloWorld` and `CkArrayEditor` → `${COMPONENT_CLASS}`
     - Dist paths from `dist/hello-world/*` or `dist/ck-array-editor/*` → `dist/${COMPONENT_SLUG}/*`

9. Lint, format, build, and test
   ```bash
   npm run lint:fix
   npm run format
   npm run build
   npm test
   ```

10. Commit and push
    ```bash
    git add -A
    git commit -m "feat: scaffold ${COMPONENT_SLUG}; update build, tests, docs"
    git push origin HEAD
    ```

## Verification Checklist

- Source
  - `src/components/${COMPONENT_SLUG}/${COMPONENT_DOT}.ts` and `${COMPONENT_DOT}.styles.ts` exist and compile.
  - `src/index.ts` exports/imports `${COMPONENT_CLASS}`.
- Tests
  - `tests/${COMPONENT_SLUG}/${COMPONENT_SLUG}.test.ts` passes locally.
- Build
  - `dist/${COMPONENT_SLUG}/${COMPONENT_SLUG}.{js,esm.js,min.js}` are generated.
- Package metadata
  - `package.json` name is `@<scope>/${COMPONENT_SLUG}`.
  - `main`/`module` point to `dist/${COMPONENT_SLUG}/*`.
  - `repository`, `bugs`, `homepage` URLs point to your new repository.
- CI/CD
  - Workflows `.github/workflows/ci.yml` and `.github/workflows/deploy.yml` present (no changes needed from template).

## Appendix: Commit References → Steps

- Initial template import
  - 38d2fb4 – Initial commit (adds template scaffolding including CI/deploy, hello‑world component, tests, configs)
- Component rename and source updates (Steps 3–5)
  - 808fbb1 – feat: rename component to ck-array-editor; add new files; update `src/index.ts` (use this pattern with your component slug)
  - 79a23fb – test: update tests to ck-array-editor; remove old test
- Build outputs and package metadata (Steps 6–7)
  - 6fd421f – build: update rollup outputs and `package.json` for ck-array-editor (parameterize `ck-array-editor` to your slug)
  - d9ba93d – fix: update package name to correct format
- Documentation (Step 8)
  - fb72f09 – docs: update README for ck-array-editor usage and paths (apply same replacements for your slug)
- Path/import corrections and polish
  - 8cb64e9 – fix: correct styles import path in `ck.array.editor.ts`
  - f983e08 – chore: lint and format after renames; update generated artifacts
  - 83e53e5 – chore: fix lint issues in styles
- CI/CD (No action required)
  - `.github/workflows/ci.yml` and `deploy.yml` unchanged since 38d2fb4

---

Remote can be checked with:

```bash
git remote -v
```
