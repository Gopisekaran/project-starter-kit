# WORKFLOW.md

The operating model for a project built from this kit. It defines where docs live,
how work is tracked, how tasks are executed, and what "done" means.

---

## 1. Docs

- Docs live in the repo under `docs/`. The structure matches the `docs-template/` skeleton
  (`overview/`, `architecture/`, `api/`, `modules/`, `pages/`, `decisions/`).
- The repo `docs/` is the **single source of truth** — API contracts, per-page specs, ADRs,
  roadmap, release notes. There is no second docs system to keep in sync.
- Docs are reviewed in PRs and updated in the **same PR as the code** they describe.
- The readable hub is the **offline docs viewer** — `pnpm docs:viewer` builds
  `docs/viewer.html`, a single self-contained page with a sidebar, search, and rendered
  tables. See §5 and [`SETUP.md`](SETUP.md).

## 2. Tracking

- Tasks and bugs live in **GitHub Issues** plus a **Projects v2 board**.
- The board has two single-select fields:
  - **Status:** Backlog → Todo → In Progress → In Review → Done
  - **Deploy:** Not deployed → Staging → Production
- Labels follow the taxonomy in [`github/labels.sh`](github/labels.sh):
  - `type:` bug | feature | chore | docs | question
  - `area:` api | web | mobile | admin | infra | docs
  - `phase:` 1 | 2
  - `priority:` P1 | P2 | P3 | P4

## 3. Execution flow

```
Create a GitHub Issue
        │
        ▼
Run it via Claude Code in the terminal
        │
        ▼
Agent implements the change (code + tests + docs)
        │
        ▼
Status updated on the board (In Review → Done)
```

Create the issue on GitHub, pull it into the terminal, let the agent implement it, then
move the board status forward as it progresses.

## 4. Definition of Done loop (IMPORTANT)

When a page, feature, or task is completed, **both** of these must happen — not just the code merge:

1. **Close the GitHub issue** — put `Closes #N` in the PR description (or move the card to
   **Done** on the board). The issue does not linger open after the work ships.
2. **Create or update the per-page doc** under `docs/pages/` — and the feature doc under
   `docs/modules/` if the change spans a whole feature.

A task is not done until the docs reflect it. Undocumented work is unfinished work.

### Definition of Documented (per-page checklist)

A page doc under `docs/pages/` is complete when it covers:

- [ ] **Route** — the URL / path and any params.
- [ ] **Purpose** — what the page is for, in one or two sentences.
- [ ] **Components** — the key components rendered and where they come from.
- [ ] **Endpoints used** — every API endpoint the page calls.
- [ ] **State** — client/server state, query keys, and what drives re-render.
- [ ] **Validations** — form/schema rules enforced on the page.
- [ ] **Edge cases** — empty, loading, error, and permission-denied states.

## 5. Docs live in the repo

All docs live in `docs/` as markdown. There is no external docs workspace, and nothing is
mirrored anywhere. Why:

- **Version-controlled** — docs move with the code. Checking out an old commit gives you
  the docs as they were at that commit.
- **Diffable** — a doc change shows up as a reviewable diff, not an opaque page edit.
- **Reviewed** — docs go through the same PR review as the code.
- **Updated with the code** — the doc change ships in the same PR as the behaviour it
  describes, so the two can't drift. A separate system always drifts, because keeping it in
  sync is a second, skippable step.
- **One account** — GitHub is the only tool anyone needs. No extra seat, login, or sync step.
- **CLI/AI friendly** — plain markdown in the working tree is directly readable by Claude
  Code and by grep.

### The readable hub: `pnpm docs:viewer`

Raw markdown in a folder is fine for grep, less fine for reading. The docs viewer
(`scripts/build-docs-viewer.mjs`) builds **`docs/viewer.html`** — one self-contained file
with every doc, marked, CSS, and JS inlined. Open it by double-clicking; it works from
`file://`, offline, with no server and no CDN.

Features: category sidebar, rendered GFM tables, full-text search with excerpts,
light/dark following the OS, and deep links (`#docs/modules/orders.md`).

`docs/viewer.html` is **git-ignored** — it's a generated artifact. Re-run `pnpm docs:viewer`
after changing docs, or you're reading a stale snapshot.

### Why not GitHub Pages

GitHub Pages sites are **public even when the repo is private** — access-controlled Pages is
a GitHub Enterprise Cloud feature. Publishing a private repo's docs to Pages would leak them.
The local viewer keeps docs private, works offline, and stays CLI/AI friendly.
