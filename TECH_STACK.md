# TECH_STACK.md

The canonical technology stack every new project adopts. Copy this file into a new
repo's `docs/` (or keep it in the starter kit) and treat it as the default. Deviate
only with a written reason recorded in an ADR under `docs/decisions/`.

---

## 1. Monorepo & Language

| Item      | Choice                | Explanation                                                                 |
| --------- | --------------------- | --------------------------------------------------------------------------- |
| Workspace | pnpm workspace        | Manage web / mobile / backend + shared code and types in one repo.          |
| Language  | TypeScript            | Type safety across all layers (backend, web, mobile, shared libs).          |

## 2. Backend

| Item                | Choice                    | Explanation                                                                                     |
| ------------------- | ------------------------- | ----------------------------------------------------------------------------------------------- |
| Framework           | NestJS                    | Modular architecture, dependency injection, guards, and pipes.                                  |
| Transaction Handling| Queue-based processing    | Batch / pull work into a queue after a threshold instead of processing individually — reliability under load. |

## 3. Web Frontend

| Item              | Choice                     | Explanation                                          |
| ----------------- | -------------------------- | ---------------------------------------------------- |
| Framework         | Next.js                    | SSR / SSG for the main application.                  |
| Marketing/Info Site | Separate Next.js public site | Dedicated landing / marketing site, decoupled from the app. |

## 4. Mobile

| Item      | Choice                  | Explanation                                    |
| --------- | ----------------------- | ---------------------------------------------- |
| Framework | Expo (React Native)     | Single codebase for iOS and Android.           |

## 5. Authentication

| Item | Choice      | Explanation                                              |
| ---- | ----------- | -------------------------------------------------------- |
| Auth | Better Auth | Sessions, login, and roles shared across web + mobile.   |

## 6. Database & ORM

| Item                | Choice                       | Explanation                                                                                       |
| ------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------- |
| DB                  | PostgreSQL                   | Primary relational data store.                                                                    |
| ORM                 | Drizzle                      | TypeScript-first ORM with typed schema and migrations.                                            |
| Data Setup          | Master-data seeding          | Seed roles, categories, and config so environments start consistent.                              |
| Data Layer Strategy | DB-vs-Redis split            | Postgres for persisted data; Redis for sessions, rate-limits, real-time counters, and queues.     |

## 7. Notifications

| Item                 | Choice                          | Explanation                                                                       |
| -------------------- | ------------------------------- | --------------------------------------------------------------------------------- |
| Email                | ZeptoMail / Resend              | Decide per project on cost, deliverability, and India support.                    |
| Mobile OTP (India)   | MSG91                           | Requires DLT template registration.                                               |
| Mobile OTP (Intl)    | Twilio Verify                   | Route India → MSG91, rest of world → Twilio.                                       |
| Push                 | In-app push notifications       | Native + web push for engagement events.                                          |
| Compliance           | GST number                      | Required for 3rd-party SMS in India.                                              |
| Templates            | Pre-approved DLT / templates    | Plan and register templates early to avoid launch delays.                         |
| Domain               | Dedicated no-reply subdomain    | Configure SPF / DKIM for deliverability.                                          |
| Email Alternative    | SMTP (Go-based / self-hosted)   | Backup / cost-saving path if managed providers get expensive.                     |

## 8. Payments

| Item           | Choice                                     | Explanation                                                          |
| -------------- | ------------------------------------------ | -------------------------------------------------------------------- |
| Gateways       | Stripe (international) / Razorpay (India)   | Route by region.                                                     |
| Receiver Types | Product owner (platform) + subscribed/individual user | Separate payout logic for the platform vs individual sellers/users. |

## 9. Realtime

| Item             | Choice                                          | Explanation                          |
| ---------------- | ----------------------------------------------- | ------------------------------------ |
| Streaming Server | Socket.io (self-hosted) vs Pusher (managed)     | Decide per project on ops vs cost.   |

## 10. Deployment

| Item    | Choice  | Explanation                                              |
| ------- | ------- | ------------------------------------------------------- |
| Hosting | Vercel  | Consistent build/host pattern across apps where possible.|

## 11. AI Dev Tooling & Documentation

| Item            | Choice                                      | Explanation                                                              |
| --------------- | ------------------------------------------- | ------------------------------------------------------------------------ |
| MCP             | Notion / GitHub / Jira                      | Connect Claude Code to the team's task and docs systems.                 |
| AI Agents       | `.claude/agents/*.md`                        | Specialized agents for frontend / backend / architecture / infra.        |
| Task Management | Notion / GitHub → terminal (Claude Code)    | Create task upstream, execute via Claude Code in the terminal.           |
| Per-page Docs   | Markdown per page/feature                   | Document API endpoints, components, routes, entry/exit, purpose, actions. |

---

## Decisions to make per project

These are genuinely open and should be locked during kickoff (record the choice in an ADR):

- **Email provider** — ZeptoMail vs Resend (weigh cost, deliverability, and India support).
- **Realtime server** — Socket.io (self-hosted, more ops control) vs Pusher (managed, less ops).
- **Payments routing** — Stripe vs Razorpay, and how region routing is decided (India → Razorpay, rest → Stripe is the default assumption).
