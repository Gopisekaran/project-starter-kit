# WORKFLOW.md

The operating model for a project built from this kit. It defines where docs live,
how work is tracked, how tasks are executed, and what "done" means.

---

## 1. Docs

- Docs live in the repo under `docs/`. The structure matches the `docs-template/` skeleton
  (`overview/`, `architecture/`, `api/`, `modules/`, `pages/`, `decisions/`).
- Docs are **optionally mirrored to a Notion workspace**. Notion is the readable hub for
  the wider team — the docs plus the roadmap / releases live there.
- The repo `docs/` is the source of truth for anything an engineer needs while coding
  (API contracts, per-page specs, ADRs). Notion is the source of truth for planning
  (roadmap, releases) and the human-friendly reading surface.

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
Create a task (Notion or GitHub Issue)
        │
        ▼
Run it via Claude Code in the terminal
        │
        ▼
Agent implements the change (code + tests)
        │
        ▼
Status updated on the board (In Review → Done)
```

Create the task upstream (Notion or GitHub), pull it into the terminal, let the agent
implement it, then move the board status forward as it progresses.

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

## 5. Docs in repo vs Notion

Keep the canonical, engineer-facing docs in the repo (`docs/`) so they version with the code
and are reviewable in PRs; mirror the readable overview, roadmap, and release notes to Notion
so non-engineers have a single readable hub. The two stay in sync through the Definition of
Done loop: whenever a PR updates a doc in `docs/`, the same change is pushed to the matching
Notion page (via the Notion MCP or a manual copy) as part of closing the task — the repo leads,
Notion follows, and neither is allowed to drift for more than one completed task.
