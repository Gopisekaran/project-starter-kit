# Project Plan — `{{APP_NAME}}`

> **Template.** Copy into the new repo's root (or `docs/`). This is the bridge between
> **docs and work**: feature docs come in, GitHub Milestones + Issues go out. Fill each
> section, delete the prompts.
>
> Written at [Stage 6](GETTING_STARTED.md#stage-6--turn-the-docs-into-a-plan), after the
> feature docs exist. Revisit it at every milestone boundary.

- **Last updated:** {{YYYY-MM-DD}}
- **Current milestone:** {{v0.1}}

## The model: milestone = version

One axis, one meaning. Don't invent a second.

| Thing | Means | Where |
|---|---|---|
| **Milestone** | A **version** that ships: `v0.1`, `v0.2`, `v1.0` | GitHub Milestone |
| **Issue** | One user story (`US-N`) from a feature doc | GitHub Issue |
| **`phase:` label** | Scope: is this live now (`phase:1`) or deferred (`phase:2`)? | Label |
| **Status / Deploy** | Where the work is right now | Projects board fields |

**Milestones sequence delivery. The `phase:` label marks scope.** They're different
questions — *when does it ship* vs *is it in the product yet* — so they get different
tools. A `phase:2` issue with no milestone is deferred scope; that's valid and expected.

Why milestones and not a doc-based roadmap: GitHub gives you the progress bar, the
burn-down, and the "what's left in v0.1" query for free, and it's attached to the issues
themselves — so it can't drift from the work the way a hand-maintained list does.

## Versioning

> How versions are numbered here. Adapt, but write it down.

- `v0.x` — pre-launch. Breaking changes are free; nothing is public.
- `v1.0` — first public release. {{Name the bar: e.g. "auth + core flow + payments, tested."}}
- `v1.x` — additive. Anything breaking waits for `v2.0`.
- Each shipped milestone gets a **git tag** (`{{v0.1}}`) and a **GitHub Release**.

## Milestones

> One block per version. Keep the table short — the issues carry the detail.

### `{{v0.1}}` — {{name, e.g. "Auth & onboarding"}}

- **Goal:** {{one sentence — what a user can do at the end that they couldn't before}}
- **Target:** {{YYYY-MM-DD or "when exit criteria pass"}}

| Feature doc | Stories | Notes |
|---|---|---|
| [`docs/features/{{auth}}.md`](docs-template/features/_template.md) | {{US-1 … US-4}} | {{…}} |
| [`docs/features/{{onboarding}}.md`](docs-template/features/_template.md) | {{US-1 … US-3}} | {{…}} |

**Exit criteria** — all must hold before the milestone closes:

- [ ] Every issue in the milestone is closed.
- [ ] CI green: lint, type-check, tests, build.
- [ ] Page docs exist for every screen shipped.
- [ ] {{feature-specific bar, e.g. "a new user can sign up and reach the dashboard on a real device"}}

### `{{v0.2}}` — {{name}}

- **Goal:** {{…}}

| Feature doc | Stories | Notes |
|---|---|---|
| {{…}} | {{…}} | {{…}} |

### `{{v1.0}}` — {{Public launch}}

- **Goal:** {{…}}
- **Additional bar:** {{security review, load test, backups tested, monitoring live}}

## Not scheduled

> Real work with no milestone yet. Parking it here — with a reason — beats silently
> dropping it or faking a date.

| Item | Why not now |
|---|---|
| {{…}} | {{…}} |

## From feature doc to issues

The decomposition, once per feature:

1. **One issue per user story.** `US-1` in the doc → one issue titled `[Feature] {{story}}`.
   If a story is too big for a couple of days, split the story in the doc first — not the
   issue. The doc stays the source of truth.
2. **Label it:** `type:feature`, `area:{{api|web|mobile|admin|infra|docs}}`, `priority:P{{1-4}}`,
   `phase:{{1|2}}`.
3. **Assign the milestone** — this is what schedules it.
4. **Link the doc.** Every issue body points at its feature doc and story id. An issue
   that doesn't say *why* is an issue someone will re-litigate in review.
5. **Add it to the board**, Status = `Backlog` or `Todo`.

```bash
# create, label, and schedule in one go
gh issue create \
  --title "[Feature] {{story title}}" \
  --body  "Implements US-1 of docs/features/{{feature}}.md" \
  --label "type:feature,area:{{web}},priority:P2,phase:1" \
  --milestone "{{v0.1}}"

# schedule an existing issue
gh issue edit {{N}} --milestone "{{v0.1}}"

# what's left in this version
gh issue list --milestone "{{v0.1}}" --state open
```

Milestones must exist before you can assign them — create them once, in the UI
(**Issues → Milestones → New milestone**) or via the API:

```bash
gh api repos/{{owner}}/{{repo}}/milestones -f title="{{v0.1}}" -f description="{{Auth & onboarding}}"
```

## When the plan changes

Requirements move. The order is fixed:

**Update the feature doc → re-plan here → adjust issues/milestones → then code.**

Never code-first. A change that lands in code but not in the doc is invisible to whoever
picks the feature up next, and the plan silently becomes fiction. See
[`WORKFLOW.md`](WORKFLOW.md#6-when-requirements-change).

- **Story changed** → edit the feature doc, then the issue.
- **Story dropped** → close the issue with a reason, strike it in the doc.
- **Story added mid-milestone** → add it to the doc; only pull it into the current
  milestone if something else moves out. Otherwise it goes to the next one.
- **Milestone slipping** → move issues out, not the date. A version means a scope.

## Related docs

- The flow: [`GETTING_STARTED.md`](GETTING_STARTED.md)
- Per-task loop: [`WORKFLOW.md`](WORKFLOW.md)
- Feature docs: [`docs-template/features/README.md`](docs-template/features/README.md)
- Board + labels: [`github/projects-board.md`](github/projects-board.md)
