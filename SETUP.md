# SETUP.md — Start a new project from this kit

Follow these steps in order to stand up a new repo from the starter kit.

---

## 1. Create the repo + pnpm workspace

Initialize a git repo and a pnpm workspace. Expected layout:

```
apps/
  api/            # NestJS backend
  web/            # Next.js main app
  marketing/      # Next.js public marketing site (optional)
  mobile/         # Expo React Native app
libs-common/      # Shared types + API handler (web + mobile)
libs-web/         # Web-only shared UI + utils
libs-mobile/      # Mobile-only shared UI + theme + utils
docs/             # Documentation
.claude/agents/   # Claude Code agents
.github/          # Issue templates + PR template
pnpm-workspace.yaml
package.json
```

Minimal `pnpm-workspace.yaml`:

```yaml
packages:
  - "apps/*"
  - "libs-*"
```

## 2. Copy the templates in

- Copy `docs-template/` → `docs/`.
- Copy `.claude/agents/` (from the kit) → `.claude/agents/`.
- Copy `github/ISSUE_TEMPLATE/` → `.github/ISSUE_TEMPLATE/`.
- Copy `github/PULL_REQUEST_TEMPLATE.md` → `.github/PULL_REQUEST_TEMPLATE.md`.
- Copy `scripts/build-docs-viewer.mjs` → `scripts/build-docs-viewer.mjs` (see step 8).

## 3. Copy the CI workflow

```bash
mkdir -p .github/workflows
cp github/workflows/ci.yml .github/workflows/ci.yml
```

Replace the `{{WEB_APP}}` / `{{ADMIN_APP}}` placeholders with the real package names, drop
any step for an app the project doesn't have, and keep the pinned pnpm version in sync with
the root `package.json` `packageManager` field. CI gates lint + type-check, tests, and build
on every push/PR to `main`. Details in [`github/workflows/README.md`](github/workflows/README.md).

## 4. Connect the GitHub MCP in Claude Code

Connect the **GitHub** MCP server so issues, PRs, and the board are reachable from the
terminal. Verify it responds before continuing (list a GitHub issue).

Docs don't need an MCP server — they're markdown in the repo, already in the working tree.

## 5. Install and authenticate the GitHub CLI

```bash
brew install gh
gh auth login                              # GitHub.com → HTTPS → browser
gh auth refresh -s project,read:project    # REQUIRED: default scopes do NOT include Projects
gh auth status                             # confirm scopes include 'project' and 'repo'
```

> The `gh auth refresh` line is not optional. The default login scopes exclude Projects, so
> every `gh project` command fails until you add them.

## 6. Create labels

Run the label taxonomy script (idempotent — uses `--force`):

```bash
bash github/labels.sh
```

## 7. Create the Projects board

Create a GitHub Projects v2 board with the **Status** and **Deploy** fields.
See [`github/projects-board.md`](github/projects-board.md) for the verified `gh project`
commands — including the two gotchas (the create call can 504 *and still create the project*,
and the built-in Status field ships with only three options).

## 8. Set up the docs viewer

The readable docs hub is a single self-contained HTML file, generated from `docs/`.

```bash
pnpm add -D -w marked
```

Add the root `package.json` script:

```json
{
  "scripts": {
    "docs:viewer": "node scripts/build-docs-viewer.mjs"
  }
}
```

Git-ignore the generated output:

```bash
echo "docs/viewer.html" >> .gitignore
```

Build and open it:

```bash
pnpm docs:viewer      # writes docs/viewer.html
open docs/viewer.html # or just double-click it
```

**What you get:** one file with marked, every doc, the CSS and the JS all inlined — so it
opens from `file://`, offline, with no server and no CDN. Category sidebar, rendered GFM
tables, full-text search with excerpts, light/dark following the OS, and deep links
(`#docs/modules/orders.md`).

The title is resolved at build time: `DOCS_TITLE` env var → root `package.json` `name` →
`"Docs"`. Override with `DOCS_TITLE="Acme Docs" pnpm docs:viewer`.

`docs/viewer.html` is a **generated artifact** and is git-ignored — regenerate it after
changing docs or you'll read a stale snapshot.

> **Why not GitHub Pages?** Pages sites are **public even when the repo is private** —
> access-controlled Pages is GitHub Enterprise Cloud only. Publishing a private repo's docs
> to Pages would leak them. The local viewer keeps docs private, works offline, and stays
> CLI/AI friendly.

## 9. Fill the Core Document in kickoff

During the kickoff meeting, fill in [`CORE_DOCUMENT.md`](CORE_DOCUMENT.md) — application name,
users, goal, problems, subscription model, platforms, success metrics, non-goals. Lock the
per-project decisions from `TECH_STACK.md` (email provider, realtime server, payments routing)
and record each in an ADR under `docs/decisions/`.

## 10. Set up CLAUDE.md

Rename `CLAUDE.md.template` → `CLAUDE.md` at the repo root and replace every `{{PLACEHOLDER}}`
with real values (app name, filter names, provider choices, env vars). This is what Claude Code
reads on every session.

---

You're ready — that's the setup done (Stage 0 of nine).

**Next: [`GETTING_STARTED.md`](GETTING_STARTED.md)** picks up here and walks the rest of the
lifecycle: discuss the product → feature docs → branding → design system → schema → plan →
implement → test → page doc, naming which agent to use at each stage.

Once you're building day-to-day, [`WORKFLOW.md`](WORKFLOW.md) is the loop you live in: create
tasks as GitHub Issues, execute them via Claude Code, and close the Definition of Done loop
(issue closed + per-page doc updated).
