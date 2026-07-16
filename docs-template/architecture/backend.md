# Backend Architecture

> **Template.** Fill each section, delete the prompts. Cite code as `<file:line>`.

## Overview

> One paragraph: framework, module count, high-level shape.

## Module map

> List the backend modules and one line on what each owns.

| Module | Responsibility |
|---|---|
| `{{users}}` | {{…}} |
| `{{orders}}` | {{…}} |

## Module structure convention

> Show the standard folder layout a module follows so new modules match.

```
module-name/
  module-name.controller.ts
  module-name.service.ts
  module-name.module.ts
  dto/       # request/response schemas + validation
  utils/     # optional module-specific helpers
```

## Guards, interceptors, pipes

> The global and opt-in cross-cutting pieces. One line each; cite code.

- **{{AuthGuard}}** ({{global/opt-in}}) — {{what it does}} — `<file:line>`
- **{{ValidationPipe}}** — {{…}} — `<file:line>`

## Background jobs / queues

> Any async work: queues, workers, schedules. Table if you have cron.

| Job | Schedule | Description |
|---|---|---|
| {{job}} | {{cron}} | {{…}} |

## Custom exceptions / error model

> Domain exceptions and the shape they serialize to. Point at the API error envelope.

- `{{SomeException}}` — `{ code, … }`, HTTP {{nnn}} — `<file:line>`

## Related docs

- System: [`system.md`](./system.md)
- Data: [`data.md`](./data.md)
- API: [`../api/README.md`](../api/README.md)
