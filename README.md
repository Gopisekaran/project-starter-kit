# Project Starter Kit

A reusable, feature-agnostic starter kit for spinning up a new product. It captures the team's
**canonical tech stack**, **operating workflow**, **kickoff template**, the **GitHub scaffolding**
(labels, issue/PR templates, Projects board, CI), and an **offline docs viewer** so every project
starts the same way.

Tracking is **GitHub-only**: docs are markdown in the repo, tasks and bugs are GitHub Issues on a
Projects v2 board. No second system to keep in sync.

This kit is domain-neutral: examples use generic `users` / `orders` and `{{APP_NAME}}` placeholders.
Nothing here is tied to any specific product.

## What's inside

| File / folder                                                | Purpose                                                                          |
| ------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| [`GETTING_STARTED.md`](GETTING_STARTED.md)                   | **Start here.** The nine-stage flow from empty repo to shipped feature, and which agent to use at each stage. |
| [`TECH_STACK.md`](TECH_STACK.md)                             | The canonical stack every project adopts, plus the per-project decisions to make. |
| [`WORKFLOW.md`](WORKFLOW.md)                                 | Operating model: docs, tracking, execution flow, the Definition of Done loop, and the change-request loop. |
| [`PROJECT_PLAN.md`](PROJECT_PLAN.md)                         | Plan template: milestones as versions, exit criteria, feature docs → issues.       |
| [`CORE_DOCUMENT.md`](CORE_DOCUMENT.md)                       | Kickoff "Application Definition" template to align the team before building.       |
| [`CLAUDE.md.template`](CLAUDE.md.template)                   | Genericized `CLAUDE.md` for a new repo — rename and fill placeholders.             |
| [`SETUP.md`](SETUP.md)                                       | Step-by-step to start a new project from this kit.                                 |
| [`github/labels.sh`](github/labels.sh)                       | `gh` script that creates the label taxonomy.                                       |
| [`github/projects-board.md`](github/projects-board.md)       | Verified `gh` + Projects v2 board setup (Status + Deploy fields), incl. the gotchas.|
| [`github/workflows/`](github/workflows)                      | GitHub Actions **CI** template (lint/type-check → test → build) + notes.           |
| [`github/ISSUE_TEMPLATE/`](github/ISSUE_TEMPLATE)            | Bug / feature / chore / docs issue templates.                                      |
| [`github/PULL_REQUEST_TEMPLATE.md`](github/PULL_REQUEST_TEMPLATE.md) | PR template with the docs + verify checklist.                              |
| [`scripts/build-docs-viewer.mjs`](scripts/build-docs-viewer.mjs) | Builds `docs/viewer.html` — a single self-contained **offline docs viewer**.   |

> Note: the `docs-template/` docs skeleton and `.claude/agents/` are referenced by `SETUP.md`
> and copied into a new repo during setup.

## Quick start

**Read [`GETTING_STARTED.md`](GETTING_STARTED.md).** It's the whole flow — set up the repo,
discuss the product, write the feature docs, define the branding and design system, design the
schema, turn it all into milestones and issues, then implement / review / test / document.

Two supporting reads: [`SETUP.md`](SETUP.md) for the setup commands, and
[`WORKFLOW.md`](WORKFLOW.md) for the day-to-day task loop once you're building.

**The one rule:** docs first, always. Everything before the plan is thinking; everything after
is typing. Skipping to the typing doesn't remove the thinking — it just relocates it into code,
where it costs more.
