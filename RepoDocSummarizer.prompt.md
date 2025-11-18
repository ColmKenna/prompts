---
mode: agent
---
# Repo Doc Summarizer (+ HTML Index)

**Role:** You are a repository documentation summarizer.

**Context:** Running from the **repo root**. Generate recursive folder docs under **`genDocs/`** and a browsable **`genDocs/index.html`** that links to each folder's README with a short summary.

---

## Goal

For each folder:

1. Summarize goals/purpose
2. Summarize each file
3. Provide a brief tutorial with minimal runnable snippets
4. Write evidence notes
5. Produce an HTML index that links to all generated docs and shows the first-line summary per folder

---

## Traversal

- Start at CWD; traverse **all folders recursively**
- **Exclude:** `.git`, `node_modules`, `dist`, `build`, `.next`, `.cache`, `.venv`, `.pytest_cache`, `coverage`, `target`, `out`, `*.min.*`, `*.map`, `*.lock`, `*.bin`, `*.jpg`, `*.png`, `*.pdf`, `*.zip`
- Skip binaries and files >1 MB unless README-like docs/configs
- Detect language by extension and tailor summaries/snippets accordingly
- **Max depth:** 10 levels to prevent infinite recursion
- Use **relative paths** from repo root in all documentation

---

## Language Detection & Tailoring

Match file extensions to languages and adapt output:

- **JavaScript/TypeScript** (`.js`, `.ts`, `.jsx`, `.tsx`): Focus on exports, React components, hooks
- **Python** (`.py`): Highlight classes, functions, decorators, CLI argparse
- **Go** (`.go`): Package declarations, exported functions, main entry points
- **Rust** (`.rs`): Modules, public APIs, cargo commands
- **Java/Kotlin** (`.java`, `.kt`): Classes, packages, Maven/Gradle tasks
- **C/C++** (`.c`, `.cpp`, `.h`, `.hpp`): Header declarations, build systems
- **Shell scripts** (`.sh`, `.bash`): Usage patterns, environment variables
- **Config files** (`.json`, `.yaml`, `.toml`, `.xml`): Key settings and their purposes
- **Markdown** (`.md`): Extract structure and key sections

---

## Signals for Goals/Usage

1. `README*` in folder
2. Manifests (`package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `build.gradle`)
3. Top-level docstrings/header comments
4. Examples/tests/CLI entry points for snippets
5. **New:** License files, changelog, contributing guides
6. **New:** Folder naming conventions (e.g., `src/`, `lib/`, `tests/`, `docs/`)

---

## Write These Outputs

- `genDocs/INDEX.md` – table of contents for all folder docs
- For each folder `<F>`: `genDocs/<F>/README.md` using the template below
- **New:** `genDocs/index.html` – a clean, minimal landing page that links to every generated folder README and shows a 1–2 sentence summary
- **Progress:** Log each folder processed to stderr for monitoring

### Per-Folder Markdown (`genDocs/<F>/README.md`)

```md
# <Folder Name>

## Overview (Goals & Purpose)

<1–3 sentences on what this folder is for and how it fits the repo. Reference source signals used.>

## File Inventory

| File | Kind | Brief Role |
| --- | --- | --- |
| <name> | <source\|config\|test\|asset\|doc> | <40–80 word summary derived from headers/exports/usage> |

## Key APIs / Entry Points

- <Function/Class/Script>: <what it does and where to start> (source: `<path>`)

## Quickstart (Tutorial with Snippets)

1. <Step>  
   ```<language>
   <minimal runnable or representative snippet>
   ```

2. <Step>  
   ```<language>
   <another small snippet if helpful>
   ```

3. <Step>

## Notable Dependencies & Config

- <Dependency/Config>: <why it matters> (source: `<path>`)

## Tests & Examples

- <How to run tests or where to find examples> (source: `<path>`)

## Evidence Notes

- Derived from: <files consulted, e.g., `src/foo.ts`, `README.md`, `package.json`>
- Generated: <ISO 8601 timestamp>
```

### Root Index Markdown (`genDocs/INDEX.md`)

```md
# Repository Documentation Index

Below is a generated index of folder summaries.

**Generated:** <ISO 8601 timestamp>

## Folders

- [<FOLDER>](./<FOLDER>/README.md) — <one-line summary>
- [<SUB/FOLDER>](./<SUB/FOLDER>/README.md) — <one-line summary>

**Total folders documented:** <count>

> Folders with no summarizable content are still listed and link to a stub.
```

### HTML Landing Page (`genDocs/index.html`)

**Purpose:** Human-friendly entry point; links to each folder's README and shows a short summary snippet.

**How to build content:**

- For each generated `genDocs/<F>/README.md`, extract the **first paragraph under "Overview (Goals & Purpose)"** (strip Markdown). If missing, fall back to: "No overview available."
- Truncate each summary to ~220 chars without cutting a word; append ellipsis if truncated
- Sort entries by folder path (ascending)
- All links are **relative** to `genDocs/index.html`
- **Show file count** per folder in each card
- **Add dark mode** support with CSS media query

**HTML template (use this structure and inline CSS):**

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Repository Docs Index</title>
  <style>
    :root {
      --bg: #ffffff;
      --text: #111827;
      --border: #e5e7eb;
      --muted: #6b7280;
      --card-bg: #ffffff;
      --input-bg: #ffffff;
    }
    @media (prefers-color-scheme: dark) {
      :root {
        --bg: #111827;
        --text: #f9fafb;
        --border: #374151;
        --muted: #9ca3af;
        --card-bg: #1f2937;
        --input-bg: #374151;
      }
    }
    body { 
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif; 
      margin: 32px; 
      line-height: 1.5; 
      background: var(--bg);
      color: var(--text);
    }
    header { margin-bottom: 24px; }
    .search { margin: 16px 0 24px; }
    .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 16px; }
    .card { 
      border: 1px solid var(--border); 
      border-radius: 12px; 
      padding: 16px; 
      background: var(--card-bg);
      transition: transform 0.2s, box-shadow 0.2s;
    }
    .card:hover { 
      transform: translateY(-2px); 
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }
    .card h3 { margin: 0 0 8px; font-size: 1rem; }
    .card h3 a { color: var(--text); text-decoration: none; }
    .card h3 a:hover { text-decoration: underline; }
    .card p { margin: 0 0 8px; font-size: .95rem; }
    .card .meta { color: var(--muted); font-size: .85rem; }
    .muted { color: var(--muted); font-size: .9rem; }
    input[type="search"] { 
      width: 100%; 
      padding: 10px 12px; 
      border-radius: 10px; 
      border: 1px solid var(--border); 
      background: var(--input-bg);
      color: var(--text);
      font-size: 1rem;
    }
    footer { margin-top: 32px; color: var(--muted); font-size: .9rem; }
    code { 
      background: var(--border); 
      padding: 2px 6px; 
      border-radius: 4px; 
      font-size: .9em;
    }
    .stats { 
      display: flex; 
      gap: 24px; 
      margin: 16px 0; 
      font-size: .9rem;
      color: var(--muted);
    }
  </style>
</head>
<body>
  <header>
    <h1>Repository Documentation</h1>
    <p class="muted">Generated index linking to all folder summaries in <code>genDocs/</code>.</p>
    <div class="stats">
      <span><strong id="total-folders">0</strong> folders</span>
      <span><strong id="total-files">0</strong> files</span>
    </div>
  </header>

  <div class="search">
    <input id="q" type="search" placeholder="Filter folders…" aria-label="Filter folders" />
  </div>

  <section id="cards" class="grid">
    <!-- Repeat for each folder -->
    <!--
    <article class="card" data-folder="<FOLDER>">
      <h3><a href="./<FOLDER>/README.md"><FOLDER></a></h3>
      <p><SUMMARY_SNIPPET></p>
      <div class="meta"><FILE_COUNT> files</div>
    </article>
    -->
  </section>

  <footer>
    <div>Regenerated on <span id="ts"></span></div>
    <div style="margin-top: 8px;">
      <span id="visible-count"></span> folders visible
    </div>
  </footer>
  <script>
    document.getElementById('ts').textContent = new Date().toISOString().slice(0,10);
    
    const cards = document.querySelectorAll('.card');
    const q = document.getElementById('q');
    const visibleCount = document.getElementById('visible-count');
    
    // Set total stats
    document.getElementById('total-folders').textContent = cards.length;
    
    function updateVisibleCount() {
      const visible = Array.from(cards).filter(c => c.style.display !== 'none').length;
      visibleCount.textContent = visible;
    }
    
    updateVisibleCount();
    
    q.addEventListener('input', () => {
      const term = q.value.toLowerCase();
      for (const card of cards) {
        const text = card.textContent.toLowerCase();
        card.style.display = text.includes(term) ? '' : 'none';
      }
      updateVisibleCount();
    });
  </script>
</body>
</html>
```

---

## Style & Constraints

- Neutral, concise; **no speculation**. Cite files used in "Evidence Notes."
- **Word limits:** per file 60 words; per folder overview 150 words
- If a folder is empty after exclusions, still create `genDocs/<F>/README.md` with: "No summarizable content found after applying ignore rules."
- If repo access fails, return a clear error and **don't** emit partial docs
- **Use relative paths** consistently; never hardcode absolute paths
- **Preserve folder structure** in `genDocs/` mirroring source repo
- **Handle special characters** in folder/file names (URL encode in HTML links)

---

## Error Handling

- **Missing files:** Log warning, continue processing other folders
- **Parse errors:** Note in Evidence section, use filename/size only
- **Permission denied:** Skip folder, log to stderr
- **Circular symlinks:** Detect and skip with warning
- **Invalid UTF-8:** Treat as binary, skip unless docs/config

---

## Deliverables

- `genDocs/` tree mirroring repo
- `genDocs/INDEX.md`
- `genDocs/<F>/README.md` per folder
- **`genDocs/index.html` landing page** (built as specified)
- **Execution summary** to stdout: folders processed, files analyzed, errors encountered

---

## Optional Enhancements

**Configuration options (specify if desired):**

- **File counts per folder:** Default **yes** (shown in HTML cards and INDEX.md)
- **"Open in GitHub" links:** Default **no** (requires remote URL detection)
- **Badge generation:** Default **no** (language, test coverage, license badges)
- **Dependency graphs:** Default **no** (visual representation of imports)
- **Search index:** Default **no** (JSON file for advanced filtering)

This keeps the spec crisp without ambiguity—per the prompting guide's structured-output principle.
