# GitHub Projects v2 Board

The team tracks tasks and bugs on a single **GitHub Projects v2** board with two
single-select fields: **Status** and **Deploy**.

## Fields

| Field  | Type          | Options                                                  |
| ------ | ------------- | ------------------------------------------------------- |
| Status | Single select | Backlog · Todo · In Progress · In Review · Done          |
| Deploy | Single select | Not deployed · Staging · Production                      |

`Status` tracks where the work is in the flow. `Deploy` tracks where that work has shipped —
so a card can be **Done** on Status but still **Staging** on Deploy until it reaches production.

---

## Create the board — CLI

Requires `gh` with the Projects scope:

```bash
gh auth refresh -s project,read:project
```

Create the project (owner can be a user or an org):

```bash
# For a user account
gh project create --owner "@me" --title "Product Board"

# For an org
gh project create --owner "your-org" --title "Product Board"
```

List projects to grab the number:

```bash
gh project list --owner "@me"
```

Add the single-select fields (replace `PROJECT_NUMBER` and `--owner`):

```bash
gh project field-create PROJECT_NUMBER --owner "@me" \
  --name "Status" --data-type SINGLE_SELECT \
  --single-select-options "Backlog,Todo,In Progress,In Review,Done"

gh project field-create PROJECT_NUMBER --owner "@me" \
  --name "Deploy" --data-type SINGLE_SELECT \
  --single-select-options "Not deployed,Staging,Production"
```

> Note: Projects v2 auto-creates a default `Status` field with `Todo / In Progress / Done`.
> Either edit that field's options in the UI to match the five above, or delete it and create
> the `Status` field with the CLI command shown. Field names must be unique.

Add an issue to the board:

```bash
gh project item-add PROJECT_NUMBER --owner "@me" \
  --url https://github.com/OWNER/REPO/issues/123
```

---

## Create the board — UI

1. Go to the org/user **Projects** tab → **New project** → **Board** layout → name it (e.g. "Product Board").
2. The board ships with a **Status** field. Open its settings and set the options to:
   `Backlog`, `Todo`, `In Progress`, `In Review`, `Done`.
3. Add a new field: **+ → New field → Single select**, name it **Deploy**, options:
   `Not deployed`, `Staging`, `Production`.
4. (Optional) Add a **Board** view grouped by `Status`, and a **Table** view for triage.
5. Enable the built-in **workflows** (Project → ⋯ → Workflows) so newly-added issues default to
   `Status: Todo` and closed issues move to `Status: Done`.
6. Add repositories to the project so their issues/PRs can be tracked.

---

## Day-to-day

- New issue → **Backlog** (or **Todo** once scoped).
- Start work → **In Progress**.
- Open PR → **In Review**; set `Deploy: Staging` when it lands on staging.
- Merge + close issue (`Closes #N`) → **Done**; set `Deploy: Production` after the prod release.
