# Design Spec: `{{Surface / Page}}`

> **Template.** Copy this file per design surface (`design/<surface>.md`). Captures
> the visual intent so implementation matches. Fill each section, delete the prompts.

## Visual direction

> The mood / feel in a few words + one paragraph. What should it evoke? Reference
> screens or inspiration if any.

## Design tokens

> The values the surface uses. Point at the source-of-truth token file if one exists.

### Color

| Token | Light | Dark | Use |
|---|---|---|---|
| `{{--color-bg}}` | {{#fff}} | {{#0a0a0a}} | {{page background}} |
| `{{--color-primary}}` | {{…}} | {{…}} | {{…}} |

### Typography

| Token | Value | Use |
|---|---|---|
| `{{--font-sans}}` | {{…}} | {{body / UI}} |
| `{{heading scale}}` | {{…}} | {{…}} |

### Spacing / radius / elevation

> The scale(s) used. One line each.

- Spacing: {{…}}
- Radius: {{…}}
- Shadow / elevation: {{…}}

## Key components

> One block per notable component: its states and behavior.

### {{Component}}

- **States:** default / hover / active / disabled / loading / error.
- **Behavior:** > …
- **Notes:** > …

## Responsive

> Breakpoints and how the layout adapts. Mobile-first.

- {{breakpoint}} → {{layout change}}

## Dark mode

> How the surface behaves across themes; anything that needs per-theme treatment.

## Motion (if any)

> Transitions/animations, durations, easing. Keep it purposeful.

## Related docs

- Frontend: [`../architecture/frontend.md`](../architecture/frontend.md)
- Page docs: [`../pages/README.md`](../pages/README.md)
