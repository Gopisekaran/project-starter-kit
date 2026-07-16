# `{{Page Name}}` — Screen Reference

> **Template.** Copy this file when documenting a new screen. Fill every section
> below — omit nothing, and write **N/A** if a section doesn't apply. Keep language
> terse, scan-friendly, fact-only. Cite code as `<file:line>`.

---

## 1. Summary

- **Route:** `{{/…}}`
- **Route file:** `<file>`
- **Primary component:** `<file>`
- **Purpose:** One sentence describing what the user does on this screen.
- **Entry points:** How users arrive (nav link / redirect / external CTA).
- **Exit points:** Where the screen sends the user next (success / cancel / error).

## 2. User journey

1. Step 1 — …
2. Step 2 — …
3. Step N — …

> Include screen-state branches (e.g. "form state", "loading state", "error state").

## 3. Fields (if form-bearing)

| Field | Label | Type | Required | Autocomplete | Validation | Error message |
|---|---|---|---|---|---|---|
| `{{email}}` | {{Email}} | email | ✓ | `email` | {{valid email}} | {{"Enter a valid email"}} |
| … | … | … | … | … | … | … |

Validation source of truth: `<file>`

## 4. APIs used

| # | Action | Method | Endpoint | Request | Response | Error codes |
|---|---|---|---|---|---|---|
| 1 | … | {{POST}} | `{{/api/…}}` | `{ … }` | `{ … }` | {{`VALIDATION_ERROR`, …}} |

> List every network call the screen makes, including optional or background ones.

## 5. State & side effects

- Server state / form state / global state usage.
- Storage / cookies touched.
- Toasts / analytics events emitted.

## 6. Validations & rules

- Client-side validation rules and where they mirror the backend.
- Business rules enforced on this screen.

## 7. Accessibility

- ARIA attributes used (`aria-required`, `aria-invalid`, `aria-describedby`, `role="alert"`, …).
- Focus management (auto-focus, trap, restoration).
- Keyboard flow: Tab order + shortcut keys.
- Screen-reader notes.

## 8. Responsive & theming

- Smallest tested viewport; layout breakpoints.
- Dark-mode / theme behavior.
- Safe-area / keyboard-overlap handling (if mobile).

## 9. Edge cases

- Double-submit / in-flight guard.
- Paste / autofill behavior.
- Slow / offline network handling.
- Already-authenticated / already-completed paths.
- Empty / error / rate-limited states.

## 10. Analytics

- Events fired and their properties.
- Where they're defined: `<file:line>`

## 11. Known gaps / TODOs

- Items scheduled for later.
- Open bugs with issue links.

## 12. Related docs

- Frontend: `../architecture/frontend.md`
- API: `../api/README.md`
- Design: `../design/_template.md`
