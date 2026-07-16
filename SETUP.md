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

## 3. Connect MCP in Claude Code

Connect the **Notion** and **GitHub** MCP servers so tasks and docs are reachable from the
terminal. Verify they respond before continuing (list a Notion page / a GitHub issue).

## 4. Install the GitHub CLI

```bash
brew install gh
gh auth login
```

## 5. Create labels

Run the label taxonomy script (idempotent — uses `--force`):

```bash
bash github/labels.sh
```

## 6. Create the Projects board

Create a GitHub Projects v2 board with the **Status** and **Deploy** fields.
See [`github/projects-board.md`](github/projects-board.md) for the `gh project` commands and UI steps.

## 7. Fill the Core Document in kickoff

During the kickoff meeting, fill in [`CORE_DOCUMENT.md`](CORE_DOCUMENT.md) — application name,
users, goal, problems, subscription model, platforms, success metrics, non-goals. Lock the
per-project decisions from `TECH_STACK.md` (email provider, realtime server, payments routing)
and record each in an ADR under `docs/decisions/`.

## 8. Set up CLAUDE.md

Rename `CLAUDE.md.template` → `CLAUDE.md` at the repo root and replace every `{{PLACEHOLDER}}`
with real values (app name, filter names, provider choices, env vars). This is what Claude Code
reads on every session.

---

You're ready. From here, follow [`WORKFLOW.md`](WORKFLOW.md): create tasks in Notion/GitHub,
execute them via Claude Code, and close the Definition of Done loop (issue closed + per-page doc updated).
