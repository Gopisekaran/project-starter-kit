# Design System — `{{APP_NAME}}`

> **Template.** One per project (not per surface — that's [`_template.md`](./_template.md)).
> Derived from [`../branding/brand.md`](../branding/_template.md): brand says *what it feels
> like*, this says *what components use*. Fill each section, delete the prompts.
>
> **This is the file the `frontend-agent` and `mobile-agent` read before writing any UI.**
> If it's wrong or stale, they'll be wrong. Keep it true.

- **Token source of truth (code):** `{{libs/ui/src/styles/tokens.css}}` / `{{libs-mobile/theme/tokens.ts}}`
- **Component library:** `{{libs/ui}}` / `{{libs-mobile/components}}`

## The one rule

**Components use semantic tokens. Never raw hex, never `--brand-*` directly.**

`--brand-primary` is identity. `--primary` is a role. Components ask for the role, so
rebranding is a token remap instead of a codebase sweep — and dark mode is free.

```
brand.md            design-system.md          component
─────────           ────────────────          ─────────
--brand-primary  →  --primary            →    <Button variant="primary">
                    --primary-fg
                    --primary-hover
```

Need a color that has no token? **Propose a token here first**, then use it. Do not
invent a one-off in a component — that's how a design system dies.

## Semantic tokens

> The roles components are allowed to reference. Every row maps back to a `--brand-*`
> source and names its use. Fill both themes — if a value is identical across themes,
> say so rather than leaving a blank.

### Surface & text

| Token | Light | Dark | From | Use |
|---|---|---|---|---|
| `--bg` | `{{…}}` | `{{…}}` | {{--brand-surface}} | {{page background}} |
| `--fg` | `{{…}}` | `{{…}}` | {{--brand-ink}} | {{primary text}} |
| `--muted` | `{{…}}` | `{{…}}` | {{…}} | {{secondary surfaces, skeletons}} |
| `--muted-fg` | `{{…}}` | `{{…}}` | {{…}} | {{secondary/helper text}} |
| `--card` | `{{…}}` | `{{…}}` | {{…}} | {{card + sheet surface}} |
| `--border` | `{{…}}` | `{{…}}` | {{…}} | {{dividers, input borders}} |
| `--ring` | `{{…}}` | `{{…}}` | {{…}} | {{focus ring — never decorative}} |

### Action & status

| Token | Light | Dark | From | Use |
|---|---|---|---|---|
| `--primary` | `{{…}}` | `{{…}}` | {{--brand-primary}} | {{primary action}} |
| `--primary-fg` | `{{…}}` | `{{…}}` | {{…}} | {{text/icon on `--primary`}} |
| `--accent` | `{{…}}` | `{{…}}` | {{--brand-accent}} | {{highlights, badges}} |
| `--destructive` | `{{…}}` | `{{…}}` | {{--brand-danger}} | {{destructive actions, errors}} |
| `--success` | `{{…}}` | `{{…}}` | {{--brand-success}} | {{success state only}} |
| `--warning` | `{{…}}` | `{{…}}` | {{…}} | {{warnings}} |

**Contrast:** every fg/bg pair above must clear **WCAG AA (4.5:1** body, **3:1** large
text and UI borders) in **both** themes. Check when you add a token, not at audit time.

## Type scale

| Step | Size / line-height | Weight | Family | Use |
|---|---|---|---|---|
| `{{display}}` | `{{…}}` | `{{…}}` | {{display}} | {{hero only}} |
| `{{h1}}` | `{{…}}` | `{{…}}` | {{sans}} | {{page title}} |
| `{{h2}}` | `{{…}}` | `{{…}}` | {{sans}} | {{section}} |
| `{{body}}` | `{{…}}` | `{{…}}` | {{sans}} | {{default}} |
| `{{small}}` | `{{…}}` | `{{…}}` | {{sans}} | {{helper, captions}} |

- **Min body size:** {{16px on mobile — smaller triggers iOS input zoom}}
- **Default weight:** {{…}} — {{state the rule; e.g. "weight is the exception, not the default"}}

## Spacing, radius, elevation

- **Spacing scale:** {{4 / 8 / 12 / 16 / 24 / 32 / 48}} — {{no arbitrary values}}
- **Radius:** `{{--radius}}` = {{…}}; {{sm/md/lg derive from it}}
- **Elevation:** {{how many levels, and what each means}}
  | Level | Value | Use |
  |---|---|---|
  | `{{shadow-sm}}` | {{…}} | {{cards at rest}} |
  | `{{shadow-md}}` | {{…}} | {{popovers, sheets}} |

## Components

> The inventory. One row per component, its states, and where it lives. A component
> that isn't here doesn't exist yet — **check this table before building a new one.**

| Component | States | Source |
|---|---|---|
| {{Button}} | default / hover / active / disabled / loading | `{{libs/ui/button.tsx}}` |
| {{Input}} | default / focus / error / disabled / readonly | `{{…}}` |
| {{Card}} | default / interactive | `{{…}}` |
| {{Badge}} | {{neutral / success / warning / danger}} | `{{…}}` |
| {{Skeleton}} | — | `{{…}}` |
| {{Empty state}} | — | `{{…}}` |

**Non-negotiables:**

- Every interactive element has a visible **focus** state using `--ring`.
- Every async action has a **loading** state and is **double-submit guarded**.
- Every list has **empty**, **loading**, and **error** states. All three, every time.
- Minimum touch target **{{44×44}}**.

## Dark mode

- **Mechanism:** {{class on `<html>` / `prefers-color-scheme` / theme provider}}
- **Default:** {{follow OS}}
- **Rule:** dark mode is a **token remap only**. If a component needs a
  `{{dark:}}` override, that's a missing token — add the token instead.
- **Watch:** {{elevation reads inverted in dark — surfaces lighten, shadows don't deepen}}

## Motion

- **Durations:** {{fast 120ms (hover) / base 200ms (enter) / slow 320ms (sheets)}}
- **Easing:** {{…}}
- **Rule:** motion explains a change of state. {{Nothing loops; nothing decorates.}}
- **Reduced motion:** respect `prefers-reduced-motion` — {{fade, don't move}}.

## Related docs

- Brand (source of the feel): [`../branding/brand.md`](../branding/_template.md)
- Per-surface specs: [`./_template.md`](./_template.md)
- Frontend architecture: [`../architecture/frontend.md`](../architecture/frontend.md)
- Page docs: [`../pages/README.md`](../pages/README.md)
