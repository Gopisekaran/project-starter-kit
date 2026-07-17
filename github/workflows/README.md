# GitHub Actions workflows

Copy these into `.github/workflows/` in the new repo:

```bash
mkdir -p .github/workflows
cp github/workflows/ci.yml .github/workflows/ci.yml
```

Then replace the `{{WEB_APP}}` / `{{ADMIN_APP}}` placeholders in `ci.yml` with the real
package names (the `"name"` field in each app's `package.json`), and drop any step for an
app the project doesn't have.

## What `ci.yml` gates

Runs on every `push` and `pull_request` to `main` (add `develop` if you run a staging
branch). A `concurrency` group cancels in-progress runs on the same ref, so a new push
supersedes the previous run instead of queueing behind it.

| Job             | Runs                                      | Depends on              |
| --------------- | ----------------------------------------- | ----------------------- |
| `lint-typecheck`| `pnpm lint` + per-app `type-check`        | —                       |
| `test`          | backend + web + admin test scripts        | —                       |
| `build`         | `pnpm build`                              | `lint-typecheck`, `test`|

`lint-typecheck` and `test` run in parallel; `build` waits for both, so a lint or test
failure fails the run before spending time on a build.

Every job follows the same setup chain:
`actions/checkout@v4` → `pnpm/action-setup@v4` (pinned to the repo's pnpm version) →
`actions/setup-node@v4` (Node 20, `cache: pnpm`) → `pnpm install --frozen-lockfile`.

Keep the pnpm version in `ci.yml` in sync with the root `package.json` `packageManager`
field — a mismatch produces lockfile errors that only appear in CI.

This is **build verification only** — it does not deploy.

## Where deploy / ops workflows go

Anything beyond verification belongs in its own workflow file next to `ci.yml`, gated on
`ci` passing and scoped with environments + secrets:

- `deploy.yml` — build and release to staging/production.
- `ops.yml` — manually-triggered (`workflow_dispatch`) operational tasks: restart, logs, cache flush.
- `db-task.yml` — manually-triggered migrations / seeds against a chosen environment.

The reference project this kit was distilled from runs exactly these four
(`ci.yml`, `deploy.yml`, `ops.yml`, `db-task.yml`). Only `ci.yml` is generic enough to
template — the rest are shaped by the target infrastructure, so write them per project.
