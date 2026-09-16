# GymLogs

A single-file HTML app for tracking your own exercises and daily weight/rep logs. No backend — everything is stored in the browser's `localStorage`.

Live file: `gymlogs-v2.html`

## What it does

- **Exercises** — a saved list of the exercises you usually do (add/remove).
- **Log a Set** — pick an exercise, enter weight + reps, it's saved against today's date.
- **Calendar** — browse any month; days with logged sets show a dot. Tapping a day scrolls down to that day's log.
- **Day Detail** — shows every set logged on the selected day, with delete per entry.

## Data model

Everything lives in the `state` object (see top of the `<script>` block) and is mirrored to `localStorage` on every change via `save()`.

```js
state.exercises = [{ id, name }]
state.logs      = [{ id, date, exerciseId, weight, reps }]
```

- `date` is a plain `YYYY-MM-DD` string.
- `weight` / `reps` can be `null` if the user only entered one of them.
- `exerciseId` links a log entry back to an exercise; deleting an exercise does **not** delete its past logs (the name will show as "Unknown" if the exercise no longer exists — worth revisiting if this becomes confusing).

### localStorage keys
| Key | Holds |
|---|---|
| `gl2-exercises` | JSON array of exercises |
| `gl2-logs` | JSON array of logs |

## Important quirk: no `alert()` / `confirm()`

This app runs inside a sandboxed artifact preview that **blocks native `alert()`, `confirm()`, and `prompt()` dialogs** — they silently do nothing. Any future "are you sure?" or validation message needs to be built in-page instead. The existing pattern to copy:

- **Tap-twice-to-confirm** for destructive actions (see `deleteExercise` / the `armedDeleteExerciseId` / `armedDeleteLogId` state flags and their chip/button rendering).
- **Inline hint text** for validation errors (see `logHint` under the Log a Set button).

If you add more delete/destructive actions, follow the same `armed*` + timeout pattern rather than reaching for `confirm()`.

## Ideas for future additions

- Weekly plan (recurring routine per weekday) — existed in an earlier version, can be reintroduced.
- Per-exercise history view (all logs for one exercise across all dates) — also existed in an earlier version.
- Editing a log entry instead of only delete + re-add.
- Rest-day / bodyweight tracking as its own log type.
- Export/import of data (was intentionally removed — reintroduce only if asked).
- Light/dark theme toggle (a light-mode `prefers-color-scheme` block already exists in the CSS but isn't user-toggleable).

## Design notes

- Font pairing: **Bebas Neue** (headings, loaded from Google Fonts) + **Inter** (body).
- Color tokens are CSS variables on `:root` — change the palette in one place.
- Mobile-first, single column, max width 540px.