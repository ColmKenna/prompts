---
mode: agent
---
# Create Repo From Template (Agent)

Create a new GitHub repository from the template at:
`https://github.com/ColmKenna/ckWebComponents.git`.

The agent should collect inputs, create the repository via GitHub CLI or REST API, clone it locally, and present a concise summary.

## Inputs (ask user first)

- New repo name (example default: `<component>-webcomponent`)
- Owner/organization (default: current GitHub user)
- Visibility: `public` or `private` (default: `private`)
- Description (optional)
- Method: `gh` (GitHub CLI) or `api` (REST)
- If `api`: GitHub token with `repo` scope
- Include all branches from template? (default: `false`)

## Prerequisites

- Git installed and configured
- GitHub account with permission to create repos
- EITHER GitHub CLI (`gh`) authenticated, OR a GitHub token for API

## Steps

1) Confirm template and derive owner
   - Template repo: `ColmKenna/ckWebComponents`
   - Derive default owner from `gh auth status` if using CLI; otherwise ask user explicitly.

2) Create repository from template
   - If using GitHub CLI (recommended):
     ```bash
     gh repo create <OWNER>/<NAME> \
       --template ColmKenna/ckWebComponents \
       --public   # or --private
     ```
     - To clone immediately: add `--clone`.

   - If using REST API:
     ```bash
     curl -s -X POST \
       -H "Accept: application/vnd.github+json" \
       -H "Authorization: Bearer <GITHUB_TOKEN>" \
       https://api.github.com/repos/ColmKenna/ckWebComponents/generate \
       -d @- <<JSON
     {
       "owner": "<OWNER>",
       "name": "<NAME>",
       "private": <true|false>,
       "description": "<DESCRIPTION>",
       "include_all_branches": false
     }
     JSON
     ```
     - Capture the `html_url` and `ssh_url` from the response for cloning.

3) Clone locally and set up main branch
   - If not already cloned:
     ```bash
     git clone https://github.com/<OWNER>/<NAME>.git
     cd <NAME>
     ```
   - Ensure main branch naming is consistent:
     ```bash
     git checkout -b main 2>/dev/null || true
     git branch -M main
     ```

4) Quick verification
   - Show remotes and default branch:
     ```bash
     git remote -v
     gh repo view --web || true
     ```

5) Output summary to user
   - New repo URL: `https://github.com/<OWNER>/<NAME>`
   - Local path: `<cwd>/<NAME>`
   - Visibility: `public|private`
   - Creation method: `gh|api`
   - Next actions (optional): run `npm install`, initialize README tweaks, or proceed with component rename using your other prompts.

## Notes

- Always use the template mechanism (do not fork or copy manually).
- The template may include a starter component; use your dedicated prompts to rename and customize after creation.
- If org policies require it, set repository features (issues/wiki) with `gh repo edit` after creation.

