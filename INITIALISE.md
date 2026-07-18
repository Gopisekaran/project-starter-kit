# Initialise a new project

**Run this first — before `SETUP.md`, before any doc, before any code.**

This kit is deliberately **app-agnostic**. It doesn't assume your app is mobile or web,
consumer or B2B, single-tenant or multi-tenant. Those are *your app's shape*, and they change
what the agents build. So the very first step is to decide them — once — and record them in an
**App Profile** that every agent then obeys.

Why this matters (the failure it prevents): if the shape lives only in your head, you spend
every agent dispatch fighting a stale default — "no, this one's desktop-first", "no, scope that
query by org". The App Profile puts the shape in the artifact, so the agents are right by
default. *Knowledge in the doc, not the session.*

---

## How to run it

Paste this into Claude Code in the fresh repo:

```
Use superpowers:brainstorming. Read INITIALISE.md, then interview me to fill
in the App Profile. Ask one axis at a time, recommend a default, and push back
if my answers contradict each other. When we're done, write the filled App
Profile into CLAUDE.md and tell me which agents, docs, and workspace folders
apply given my answers.
```

The interview is short — nine axes. Its output is the **App Profile** block in `CLAUDE.md`
(the source of truth every session reads) plus a list of what applies.

---

## The App Profile — the nine axes

For each: what it decides downstream, and the options. The **bold** option is the kit's default
if you have no reason to differ.

### 1. Surfaces — which apps exist?

*Decides which agents apply, the `apps/*` workspace layout, and the CI matrix.*

Pick all that apply: **`web`** · `admin` · `mobile` · `marketing` · `api-only`

- `mobile` present → the **mobile-agent** and the Expo stack apply. Absent → they're skipped
  entirely; ignore every "mobile" mention in the kit.
- `api-only` (a backend with no first-party UI) → frontend/mobile agents don't apply; the API
  contract *is* the product.

### 2. Form factor — what's the primary layout intent?

*Decides the frontend-agent's layout rules: navigation pattern, density, breakpoint direction, and
how hard responsiveness is enforced. Not a binary — pick the one that matches how it's actually used.*

- **`desktop-first`** — dense, sidebar navigation, information-rich; the desktop is primary and
  small screens are a graceful fallback. For tools people use at a desk for hours (dashboards, admin, B2B).
- `responsive` — **no single primary size; the layout adapts fluidly and is equally good on phone,
  tablet, and desktop.** Sidebar collapses to a drawer, grids reflow, nothing is an afterthought.
  For broad-audience web apps, content sites, and anything a user might open on any device.
- `mobile-first` — phone-primary web/PWA: bottom-nav, thumb-reachable, one-thing-per-screen, layer
  `sm: md: lg:` *up*. Desktop is a widened phone. For consumer web apps used mostly on a phone.
- `mobile-only` — **the product is the mobile app** (`mobile` in Surfaces, web absent or a thin
  marketing shell). Layout is native patterns, not web breakpoints; the frontend-agent barely applies.

> Independent of Surfaces, except `mobile-only` implies `mobile` is the main surface. A `web`-only
> app can be any of `desktop-first` / `responsive` / `mobile-first`. **Choose by who uses it and
> where** — "our users are on phones half the time" → `responsive`, not `desktop-first`-with-regrets.

### 3. Tenancy — who owns the data?

*The biggest one. Decides the backend data-access pattern, `CurrentUserPayload`, and the schema.*

- `single-tenant` — one user = one account, data belongs to the user (B2C: a marketplace, a
  social app, a fitness tracker). Queries use `db` directly.
- `multi-tenant` — users belong to an **organisation**, and data is scoped to it (B2B: a PMS, a
  CRM, an internal tool). **Every** query is scoped via a `forOrg(orgId)` helper, with the API
  tier as the enforcement wall. `CurrentUserPayload` carries the active org + org-role.

> Multi-tenant is a one-way door — retrofitting an org boundary later touches every table and
> every query. Decide it now, honestly. If two companies' data must never mix, you're
> multi-tenant.

### 4. Tenant unit *(multi-tenant only)* — what's the org called?

*The word that appears in the schema (`org_id`), the URLs, and the UI.*

`org` · `firm` · `workspace` · `team` · `account` · `tenant` — pick the one your users would say.

### 5. Audience — who is this for?

*Informs density, tone, and the branding defaults the design system starts from.*

- `consumer` — public, varied skill, mobile-heavy, warmth matters.
- `professional / internal` — trained users, efficiency over hand-holding, density is a feature.

### 6. Localisation — one language or many?

*Decides whether the i18n stack (next-intl / message catalogs) is wired at all.*

- **`english-only`** (or any single language) — no i18n framework; ship plain strings.
- `i18n` — list the locales. The frontend-agent wires next-intl; navigation goes through the
  locale-aware router; strings live in message catalogs.

> Don't add i18n "to be safe". It taxes every string and every route. Add it when you have a
> second locale, not before.

### 7. Realtime — do you need live updates?

*Decides whether the realtime transport + provider is wired.*

- **`none`** — request/response only. Most apps start here.
- `pusher` (managed) · `soketi` (self-hosted, Pusher-protocol) · `socket.io` (self-hosted) —
  see the infra-deployment-agent for the trade-offs.

### 8. Integrations — what's switched on at launch?

*Decides which provider env vars and adapters exist. Everything defaults off.*

- **Email:** `resend` · `zeptomail` · `none`
- **Payments:** `stripe` · `razorpay` · `none`
- **Push:** `fcm` · `none`
- **SMS/OTP:** `msg91` (India) · `twilio` (intl) · `none`

### 9. Testing depth — how far up the pyramid at launch?

*Decides what the testing-agent writes and what CI gates. Unit + integration + the coverage bar are
the floor for every project; the variable is E2E.*

- `standard` — Vitest unit + Supertest integration, **80% coverage / 100% on critical paths**. The
  minimum; a pure API or early internal tool can start here and add E2E later.
- **`full`** — everything in `standard` **plus Playwright E2E** (Maestro for mobile) on the critical
  journeys (auth, checkout, the core flow). The default for anything user-facing headed to production.

> Don't defer E2E on a public product "until later" — the critical-path journey test is the one that
> catches the regression that actually reaches a user. Deferring is for API-only or pre-alpha, not
> for "we're busy."

---

## What the answers configure

Once the App Profile is filled, here's the mapping the agents follow. This is also what the
interview should read back to you at the end.

| Axis answer | Effect |
|---|---|
| `mobile` in Surfaces | mobile-agent + Expo apply; else skipped |
| no `web`/`admin`/`mobile` (api-only) | frontend-agent + mobile-agent skipped |
| `desktop-first` | frontend-agent: sidebar, dense, desktop-down breakpoints |
| `responsive` | frontend-agent: fluid/adaptive layout, drawer-collapsing nav, equal at every breakpoint |
| `mobile-first` | frontend-agent: bottom-nav, thumb-first, mobile-up breakpoints |
| `mobile-only` | frontend-agent barely applies; mobile-agent + native patterns lead |
| `single-tenant` | backend-agent: `db` directly; no org column |
| `multi-tenant` | backend-agent: `forOrg(orgId)` on every query; `org_id` FK + composite indexes; `CurrentUserPayload` gains org + role; API tier is the wall |
| `english-only` | frontend-agent: no i18n; plain strings |
| `i18n` | frontend-agent: next-intl, locale router, message catalogs |
| `realtime ≠ none` | infra-deployment-agent wires the chosen transport |
| each integration | its adapter + env vars exist; the rest stay off |
| Testing `standard` | testing-agent: unit + integration + coverage bar; no E2E yet |
| Testing `full` | testing-agent: adds Playwright E2E (Maestro on mobile) on critical journeys; CI gates it |

---

## After the interview

1. The App Profile block is written into `CLAUDE.md` (see `CLAUDE.md.template`).
2. Continue with **[`SETUP.md`](SETUP.md)** — create only the `apps/*` your Surfaces named.
3. Then **[`GETTING_STARTED.md`](GETTING_STARTED.md)** for the full nine-stage lifecycle.

> **Requirements shift the shape later?** (e.g. you go single- → multi-tenant, or add a second
> locale.) That's a real architectural change: update the App Profile in `CLAUDE.md`, write an
> ADR recording why, refresh the agents' context, *then* build. Never let the code lead the
> profile — same rule as [`WORKFLOW.md § 6`](WORKFLOW.md).
