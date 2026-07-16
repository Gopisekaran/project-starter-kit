# Frontend Architecture

> **Template.** Fill each section, delete the prompts. Cite code as `<file:line>`.

## Overview

> One paragraph: framework, rendering model, state management, styling.

## Routing

> How routes are structured (file-based / config), route groups, and any layout
> hierarchy. Note any locale/i18n prefixing.

- Route groups: {{e.g. (auth), (main), (onboarding)}}
- {{convention}} — `<file:line>`

## App state & guards

> The top-level state machine that decides what a user sees (guest / onboarding /
> active / blocked). One line per state.

- **{{GUEST}}** → {{…}}
- **{{ACTIVE}}** → {{…}}

## Data fetching & client state

> How the frontend talks to the API (query lib, generated hooks), and where global
> client state lives.

- Server state: {{e.g. React Query}} — `<file:line>`
- Client/global state: {{e.g. Redux / context}} — `<file:line>`

## Styling & design system

> Component library, styling approach, theming (light/dark). Link to design specs.

- Component library: `{{@your/ui-components}}`
- Theming: {{…}} — see [`../design/_template.md`](../design/_template.md)

## Real-time / push (if any)

> How the client receives live updates (websocket/SSE/push). One line; cite code.

## Related docs

- System: [`system.md`](./system.md)
- Pages: [`../pages/README.md`](../pages/README.md)
- Design: [`../design/_template.md`](../design/_template.md)
