# {{PROJECT_NAME}} Documentation

> **Template.** This is the docs index for a new project. Replace `{{PLACEHOLDERS}}`,
> delete the prompt lines (`> …`), and keep the structure. Generic examples use
> resources like `users` and `orders` — swap in your own domain.

This is the engineering documentation for **{{PROJECT_NAME}}** — {{ONE_LINE_DESCRIPTION}}.

> Describe, in one or two lines, what state this documentation reflects (e.g. what
> is live vs. what is planned/deferred). If you use phases or milestones, name the
> current one and where future-state specs live.

## Reading these docs: `pnpm docs:viewer`

These docs are markdown in the repo — that's the single source of truth. For a readable hub,
build the offline viewer:

```bash
pnpm docs:viewer      # writes docs/viewer.html
open docs/viewer.html # or just double-click it
```

It's **one self-contained file** (marked + every doc + CSS + JS inlined), so it works from
`file://`, offline, with no server and no CDN. You get a category sidebar, rendered GFM
tables, full-text search with excerpts, light/dark following the OS, and deep links
(`#docs/modules/orders.md`).

`docs/viewer.html` is git-ignored — it's generated. **Re-run `pnpm docs:viewer` after any doc
change**, or you're reading a stale snapshot.

> **Why not GitHub Pages?** Pages sites are public even when the repo is private —
> access-controlled Pages is GitHub Enterprise Cloud only. For a private repo, publishing docs
> to Pages would leak them. The local viewer keeps docs private and works offline.

## Where to start

- **New to the codebase?** → [`overview/product.md`](./overview/product.md) then [`architecture/system.md`](./architecture/system.md)
- **Looking up an API?** → [`api/README.md`](./api/README.md)
- **Trying to understand a feature?** → [`modules/`](./modules/)
- **Wondering why we made a decision?** → [`decisions/`](./decisions/)
- **Working on a screen?** → [`pages/`](./pages/)
- **Operations/runbooks?** → [`operations/`](./operations/)
- **Design specs?** → [`design/`](./design/)

## Quick facts

> A scannable at-a-glance table of the product's current reality. One row per topic
> a new engineer would ask about (surfaces, auth, key limits, what's on/off).

| Topic | Reality |
|---|---|
| Surfaces | {{e.g. web, mobile, admin}} |
| Auth | {{how users authenticate}} |
| {{Topic}} | {{Value}} |

## Structure

```
docs/
├── overview/        # what this product is and who it's for
├── architecture/    # how the system is built
├── api/             # endpoints, auth, conventions, per-module
├── modules/         # cross-cutting feature deep-dives
├── decisions/       # Architecture Decision Records (ADRs)
├── operations/      # production runbooks
├── pages/           # per-screen docs (one file per screen)
└── design/          # design specs (visual direction, tokens, components)
```

## Conventions

- **Cite code for every non-trivial claim.** Format: `<file-path>:<line>`
  (e.g. `src/modules/users/users.service.ts:42`). A doc claim without a code
  citation is an opinion, not a fact.
- **`[ASSUMPTION]` markers** flag a claim not yet confirmed by a developer. They
  should not exist in finalized docs; each one is a bug or a tracked open question.
- **ADRs are immutable once accepted.** To change a decision, write a new ADR that
  supersedes the old one — never edit the accepted record. See [`decisions/`](./decisions/).
- **Per-page discipline.** Every screen gets its own doc in [`pages/`](./pages/),
  updated whenever that screen is completed or changed. Copy [`pages/_template.md`](./pages/_template.md).
- **Docs link to future-state specs** when a feature is deferred — they don't
  pretend the feature doesn't exist.

---

*Last verified {{YYYY-MM}} against `{{BRANCH_OR_SHA}}`. If you find a discrepancy, this doc is wrong — open an issue or update it.*
