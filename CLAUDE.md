# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is the **starter kit itself**, not a project built from it. There is no application code,
no `package.json`, no workspace, no tests. Everything here is **source material that gets copied
into a new repo** during `SETUP.md`, plus the prose that explains the process.

The practical consequence: when asked to "add a feature", "fix the API", or "update the schema",
you are almost certainly being asked to change a *template or a doc* — not to write app code here.
If a request seems to want real NestJS/Next.js/Expo code in this repo, confirm first whether the
user means this repo or a project generated from it.

`CLAUDE.md.template` is **not** this file's source — it's the payload delivered to new repos
(renamed to `CLAUDE.md` there). Editing one does not imply editing the other; they serve
different readers.

## Commands

There is no build, lint, or test in this repo. The single executable is the docs viewer builder,
which is a **template for downstream repos** — it reads `docs/` and `package.json` at its repo
root, neither of which exists here, so it is not meaningfully runnable in place:

```bash
node scripts/build-docs-viewer.mjs    # downstream: pnpm docs:viewer → docs/viewer.html
```

It requires `marked` (resolved via `createRequire`) and is wired downstream by `SETUP.md` step 8.

## The two kinds of files here

| Kind | Files | Rule when editing |
|---|---|---|
| **Process prose** — read by humans, stays in the kit | `README.md`, `INITIALISE.md`, `GETTING_STARTED.md`, `SETUP.md`, `WORKFLOW.md`, `TECH_STACK.md` | Must stay internally consistent — see the cross-reference web below |
| **Templates** — copied into a new repo | `CLAUDE.md.template`, `PROJECT_PLAN.md`, `CORE_DOCUMENT.md`, `docs-template/**`, `.claude/agents/**`, `github/**`, `scripts/**` | Must stay placeholder-driven and domain-neutral |

`github/` is deliberately **not** `.github/` — the kit's own repo must not activate its own CI,
issue templates, or PR template. `SETUP.md` step 2–3 copies it to `.github/` downstream. Do not
"fix" this by renaming the folder.

## The App Profile is the kit's central mechanism

The kit is stack-fixed but **shape-neutral**. The shape (surfaces, form factor, tenancy, tenant
unit, audience, localisation, realtime, integrations, testing depth — nine axes) is decided once
by the `INITIALISE.md` interview and written into the downstream `CLAUDE.md`. Every agent reads
that block and obeys it over its own examples.

This is why nothing in the kit may assume a shape. Concretely, when writing or editing any
template or agent file:

- Never assume mobile exists, that the app is multi-tenant, that i18n is wired, or that realtime
  is on. Gate each behind its axis (`*(only if 'mobile' in Surfaces)*`, `per App Profile`, …).
- Use generic domain examples — `users`, `orders`, `{{APP_NAME}}`. No real product names.
- Placeholders are `{{DOUBLE_BRACE}}`. Keep them; downstream setup replaces them. Filling one in
  with a plausible value silently bakes in a default the user never chose.

The nine axes and their downstream effects are tabulated in `INITIALISE.md`; the mirror table of
the same axes lives at the top of `CLAUDE.md.template`. **Changing an axis means editing both**,
plus any agent file that branches on it.

## Cross-reference web (edit one, check the others)

These files reference each other by name and by step number. A change in one usually needs a
matching change elsewhere:

- **Nine-stage lifecycle** — defined in `GETTING_STARTED.md` (Stages 0–9, plus the cheat sheet
  mapping each stage to an agent). `README.md`, `SETUP.md`, and `WORKFLOW.md` all point into it.
  Renumbering a stage breaks every inbound reference.
- **Entry order** — `INITIALISE.md` → `SETUP.md` → `GETTING_STARTED.md` → `WORKFLOW.md`. This
  order is asserted in the header of nearly every file. Don't reorder it in one place only.
- **The seven agents** — listed in `GETTING_STARTED.md`'s cheat sheet and in `CLAUDE.md.template`'s
  Pointers. Adding or removing one under `.claude/agents/` means updating both tables.
- **Frontend UI architecture** — the models live in `.claude/agents/references/`
  (`error-architecture.md`, `form-templates.md`, `page-templates.md`); `frontend-agent.md` routes
  to them (§2.6, §3.0–3.2), `review-agent.md` checks them, `testing-agent.md` tests them,
  `CLAUDE.md.template`'s Web section states them in one line each, and
  `docs-template/architecture/frontend.md` is where a project records which archetypes and error
  surfaces it actually chose. A rule added to a reference file must reach the agent that enforces
  it, or it's advice nobody applies. `SETUP.md` step 2 must keep copying `references/`.
- **`docs/` folder taxonomy** — `overview/ features/ branding/ design/ architecture/ api/ modules/
  pages/ decisions/ operations/`. Defined by `docs-template/`, restated in `WORKFLOW.md § 1`
  (including the "which of the four gets confused" table) and in `docs-template/README.md`'s
  navigation. Adding a folder means adding it to all three, and to the `CATEGORY_META` ordering
  in `scripts/build-docs-viewer.mjs`.
- **Workspace layout** — the `apps/*` + `libs-*` menu appears in both `SETUP.md` step 1 and
  `CLAUDE.md.template`. Keep them identical.
- **Tracking model** — milestones-as-versions, `phase:` as scope, the Status/Deploy board fields,
  and the label taxonomy are stated in `WORKFLOW.md § 2` and § 7, `PROJECT_PLAN.md`, and enforced
  by `github/labels.sh` + `github/projects-board.md`. The label list in prose must match the script.

## Editorial conventions the existing prose follows

Match these — the kit's voice is part of its product:

- Every rule states **the failure it prevents**, not just the rule. ("`db:push` is banned" is
  followed by *why* — it desyncs migration history.) A rule without a consequence gets ignored.
- Blockquote callouts (`>`) carry the warnings, the one-way doors, and the "don't do this to be
  safe" advice. Prefer them to bolded inline scolding.
- Tables carry decisions and mappings; prose carries reasoning. Don't invert this.
- Line width in the markdown source wraps around ~100 chars.
- Ordering is deliberate: options lists put the **bold default first** with the reason to differ.

## Hard invariants in the templated stack

These are stated in `TECH_STACK.md` and `CLAUDE.md.template` and are load-bearing — don't soften
them when editing templates or agent files:

- **Drizzle migrations only; `db:push` is banned** — there is no such script downstream.
- **UUIDv7 primary keys**, not auto-increment or hand-rolled strings.
- **Statuses/enums live in lookup tables + FK**, not `text` enums or `pgEnum`.
- **Better Auth owns its `user` / `session` / `account` / `verification` tables** (singular) —
  extend, never rename.
- **Context7 MCP is required** before using an unfamiliar or fast-moving library API — the agents
  are all instructed to look it up rather than recall it.
- **Docs ship in the same PR as the code they describe**; a task isn't done until the issue is
  closed *and* the page doc exists (`WORKFLOW.md § 4`).
- **Requirement changes go doc-first** — feature doc → re-plan → implement → test → page doc.
  Never code-first (`WORKFLOW.md § 6`).

## Pointers

- `README.md` — the index of what's in the kit and what each file is for.
- `INITIALISE.md` — the App Profile interview (the shape decision).
- `GETTING_STARTED.md` — the nine-stage lifecycle and the agent-per-stage cheat sheet.
- `SETUP.md` — the ten setup steps for a new repo.
- `WORKFLOW.md` — day-to-day loop, Definition of Done, change-request loop.
- `.claude/agents/*.md` — the seven specialists, each scoped to what it will and won't touch.
- `.claude/agents/references/*.md` — frontend UI architecture the agents read: error boundaries and
  the error taxonomy, form structure, page archetypes.
