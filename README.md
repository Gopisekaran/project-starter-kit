# Project Starter Kit

A reusable, feature-agnostic starter kit for spinning up a new product. It captures the team's
**canonical tech stack**, **operating workflow**, **kickoff template**, and the **GitHub scaffolding**
(labels, issue/PR templates, Projects board) so every project starts the same way.

This kit is domain-neutral: examples use generic `users` / `orders` and `{{APP_NAME}}` placeholders.
Nothing here is tied to any specific product.

## What's inside

| File / folder                                                | Purpose                                                                          |
| ------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| [`TECH_STACK.md`](TECH_STACK.md)                             | The canonical stack every project adopts, plus the per-project decisions to make. |
| [`WORKFLOW.md`](WORKFLOW.md)                                 | Operating model: docs, tracking, execution flow, and the Definition of Done loop. |
| [`CORE_DOCUMENT.md`](CORE_DOCUMENT.md)                       | Kickoff "Application Definition" template to align the team before building.       |
| [`CLAUDE.md.template`](CLAUDE.md.template)                   | Genericized `CLAUDE.md` for a new repo — rename and fill placeholders.             |
| [`SETUP.md`](SETUP.md)                                       | Step-by-step to start a new project from this kit.                                 |
| [`github/labels.sh`](github/labels.sh)                       | `gh` script that creates the label taxonomy.                                       |
| [`github/projects-board.md`](github/projects-board.md)       | How to create the Projects v2 board (Status + Deploy fields).                      |
| [`github/ISSUE_TEMPLATE/`](github/ISSUE_TEMPLATE)            | Bug / feature / chore / docs issue templates.                                      |
| [`github/PULL_REQUEST_TEMPLATE.md`](github/PULL_REQUEST_TEMPLATE.md) | PR template with the docs + verify checklist.                              |

> Note: the `docs-template/` docs skeleton and `.claude/agents/` are referenced by `SETUP.md`
> and copied into a new repo during setup.

## Quick start (8 steps)

1. **Create the repo + pnpm workspace** — `apps/{api,web,marketing,mobile}`, `libs-*`.
2. **Copy templates in** — `docs-template/` → `docs/`, `.claude/agents/`, and `github/` templates → `.github/`.
3. **Connect MCP** — Notion + GitHub in Claude Code.
4. **Install the CLI** — `brew install gh && gh auth login`.
5. **Create labels** — `bash github/labels.sh`.
6. **Create the Projects board** — Status + Deploy fields (see `github/projects-board.md`).
7. **Fill `CORE_DOCUMENT.md`** — during kickoff; lock the per-project decisions.
8. **Set up `CLAUDE.md`** — rename `CLAUDE.md.template` → `CLAUDE.md` and fill placeholders.

Full detail in [`SETUP.md`](SETUP.md). Day-to-day process in [`WORKFLOW.md`](WORKFLOW.md).
