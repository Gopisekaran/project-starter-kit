# COORDINATION.md

How to work this repo when you are **one of several sessions** — human or agent — touching it at
the same time. [`WORKFLOW.md`](WORKFLOW.md) is the loop for one worker; this file is what changes
when there are two or more.

> Most parallel-work collisions are avoidable, but only if you isolate and behave correctly **from
> the first command**. The expensive failures — a shared branch that won't build, two migrations
> with the same number, a database two sessions corrupted together — are all set up in the first
> five minutes and paid for hours later. Read this before you start, not after the collision.

These rules assume nothing beyond the kit's stack (Postgres · Redis · Drizzle · pnpm · GitHub).
Gate the stack-specific parts on your **App Profile** — no Redis line if you don't run Redis, no
mobile ports if `mobile` isn't a surface.

---

## 1. Isolate before you start

This one prevents the entire worst class of failure. Do it first.

- **A separate working copy per session.** Never run two sessions in the same working directory —
  they overwrite each other's uncommitted files and fight over git's index and `HEAD`. Use a
  `git worktree` (one clone, many working dirs) or a separate clone:

  ```bash
  git worktree add ../myapp-s2 -b feat/123-invite-flow
  cd ../myapp-s2 && pnpm install      # each worktree needs its own install — pnpm links per dir
  ```

- **Separate runtime state.** If the project runs local services, give each working copy its own,
  on offset ports, with its own `.env`. Two sessions sharing one Postgres corrupt each other's
  schema and data; two sharing one Redis cross their queues and rate-limit counters.

  | Var | Session 1 | Session 2 |
  |---|---|---|
  | `DATABASE_URL` | `…/app_dev` | `…/app_dev_s2` (its own database) |
  | `REDIS_URL` | `…/0` | `…/1` (its own logical DB, or its own port) |
  | app port(s) | `3000` / `4000` | `3100` / `4100` |
  | `BETTER_AUTH_URL`, `FRONTEND_URL`, `ADMIN_URL` | match the ports above | match the offset ports |

  > **Offset *everything together*.** The auth URL, the CORS origins, and the public API URLs
  > (`NEXT_PUBLIC_API_URL`, `EXPO_PUBLIC_API_URL`) all derive from the ports. Miss one reference and
  > you get a failure that looks like something else entirely — a CORS error that reads as an auth
  > bug, a login that half-works. Change the port in one file, change it in all of them.

  Record your chosen offsets in the mailbox (§7) so the next session doesn't pick the same ones.

- **Own identity.** Set the git remote and commit identity for this session explicitly; don't
  inherit another account's `user.email` from a shared clone.

---

## 2. The serialized resource is the migration track — treat it as a lock

Most repos have exactly one artifact that is **ordered and append-only** and therefore cannot be
written by two sessions at once. In this stack that's the **Drizzle migration series** under
`apps/api/src/db/migrations/`. (The same applies to `pnpm-lock.yaml`, `PROJECT_PLAN.md`, and the
seed track — anything numbered or append-only.)

The migrations-only rule you already follow (`TECH_STACK.md`, `backend-agent.md §4.4`) exists for
history integrity. Under parallel work it is also a **concurrency lock**: one writer at a time,
across all sessions.

- **Pull latest immediately before you `pnpm db:generate`.** Then commit + push the migration the
  moment it exists — don't sit on it while you write the feature around it.
- **If another session's migration landed while you worked**, pull, drop your generated file, and
  **re-generate so yours comes after theirs**. Never hand-edit two files to share a slot.

> Two concurrent `db:generate`s produce migrations with colliding numbers and a shared branch that
> won't migrate cleanly for anyone. Untangling that costs far more than the ten seconds of "pull,
> then generate" — and it's the single most common way parallel sessions break each other here.

Claim the lock visibly in the mailbox (§7) while you hold it.

---

## 3. Verify against a clean state, not your evolved local one

A change that works on your incrementally-modified local environment can still be broken from
scratch. Before you push anything that touches shared or ordered state, reproduce it from zero:

```bash
# fresh database, migrations from the start, master data — the state everyone else will have
pnpm db:migrate && pnpm db:seed
```

> Your local DB has hours of accumulated rows, hand-run statements, and half-reverted experiments
> that **won't exist for the next session or in CI**. If your change only works against that
> sediment, it isn't done — it's a bug with a delay on it. "Works on my DB" is not "works."

---

## 4. Never validate on surgically-modified state

After you manually edit an ordered artifact, renumber a migration, or hand-fix a row, your local
runtime is a hybrid that belongs to no one. **Reset it to clean before you run the suite:**

```bash
# drop + recreate the database, re-migrate, THEN run tests
pnpm db:migrate && pnpm db:seed && pnpm test
```

> Running Vitest / Supertest / Playwright against a hand-patched database gives you false failures
> (state the tests didn't expect) and false passes (state that masks the bug) — both send you
> chasing ghosts. Rebuild from scratch first, every time you've touched state by hand.

---

## 5. Stay in your lane

Agree who owns what **before** anyone starts, and don't edit another session's files.

- **Lanes map to the `area:` labels** (`api` · `web` · `mobile` · `admin` · `infra` · `docs`) and,
  within the backend, to a module under `apps/api/src/modules/`. One session per lane is the
  simplest split that avoids merges.
- **Read shared code; don't modify it.** The shared surfaces are `libs-common/`, `libs-web/`,
  `libs-mobile/`, the **App Profile in `CLAUDE.md`**, and root config (`package.json`,
  `pnpm-workspace.yaml`, `tsconfig`, `.prettierrc`). A change there hits every lane at once.
- **When you genuinely must touch a shared file**, expect a merge and resolve it by **keeping both
  sides' additions** — never overwrite the other session's work. Flag it in the mailbox first (§7).

> The App Profile is the sharpest case: it's the source of truth every agent reads, so two sessions
> editing it in parallel don't just conflict — they change what the *other* session is building
> mid-flight. Treat an App Profile change as a stop-the-world event (see `WORKFLOW.md § 6`).

---

## 6. Shared-branch hygiene

- **Pull before you start; push when you stop.** Never sit on unpushed commits — another session
  can't pull what's only on your disk, and the longer it sits the worse the eventual merge.
- **Stage by explicit path. Never `git add -A`** in a shared working copy — you'll sweep up another
  session's in-flight files (and, as the PR template already warns, a stray `.env`).
- **CI is the gate.** Never push a state you haven't verified from clean (§3), and never gate the
  push on a check you didn't let finish. Green locally ≠ green on a clean CI runner.
- **When your commit and a teammate's touch the same file, rebase and merge additively** — keep
  both changes; resolve, don't overwrite.

This extends `GETTING_STARTED.md` Stage 7 (one issue → one branch → one PR): under parallel work,
that branch also gets **its own working copy** (§1), and the PR merges only after a clean-state CI
pass.

---

## 7. Coordinate through a durable channel, not chat

Decisions and handoffs between sessions must live **in the repo**, not in a message a human relays.
Relayed instructions go stale and leave no record.

- **The mailbox is `docs/coordination.md`** (from `docs-template/coordination.md`). It holds the
  active-session registry, the offset registry (§1), the resource locks (§2, §5), and the handoff
  notes. Commit it like any other change.
- **The GitHub Issue's *In Progress* status is your public lane claim** — moving a card to In
  Progress tells every other session that lane is taken. Don't pick up a card someone else has In
  Progress.
- **The PR description is the durable record** of what a branch did and what it touched.

> **Reader clears the note when consumed.** A handoff that stays after it's been acted on looks
> current forever, so the next reader trusts a lie. If you did what a note asked, delete it in the
> same commit. A stale mailbox is worse than an empty one.

---

## 8. Report faithfully

State what you **actually** verified, and on what environment.

- Distinguish **"passed clean"** (fresh DB, migrations from zero, §3) from **"passed on my
  machine"** (your evolved local state). They are different claims; only the first means it's done.
- If tests failed, say so, with the output. A skipped step is reported as skipped.
- Name the environment: which database, which branch, whether CI was green on a clean runner.

> This is the same honesty the kit asks for everywhere (`WORKFLOW.md § 4` — a task isn't done until
> it's verified *and* documented). Parallel work just raises the cost of a false "it's green": the
> next session builds on your claim.

---

## The five-minute setup checklist

- [ ] Own working copy — `git worktree` or a fresh clone; never share a directory.
- [ ] Own `.env` — offset `DATABASE_URL`, `REDIS_URL`, ports, and every URL that derives from them.
- [ ] Offsets recorded in `docs/coordination.md`; own git identity set.
- [ ] Lane agreed and claimed (issue → In Progress, row in the mailbox).
- [ ] `git pull` before the first change; `pnpm install` in the new worktree.

**Related:** [`WORKFLOW.md`](WORKFLOW.md) § 8 (the pointer here and the two invariants) ·
[`GETTING_STARTED.md`](GETTING_STARTED.md) Stage 7 (the single-worker branch flow this extends) ·
[`backend-agent.md`](.claude/agents/backend-agent.md) § 4.4 (migrations, at the point you run
`db:generate`) · `docs/coordination.md` (the live mailbox, per project).
