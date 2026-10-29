# ResidentFlow

A personal residency planner for one PGY2 internal medicine resident. It answers one question every day: what is my one study job today, given the rotation I am on?

Live app (private, sign-in required): https://claude.ai/artifact/1fRAEuyTcWiVgtevZq6jYr

## What phase 1 does

- **Today**: rotation, day type (night shift, on call, post-call, off, pulled in), and one study job: N questions in an assigned module of the AMBOSS Internal Medicine (ABIM) study plan, with a button that opens that module and a fixed time slot. One tap to log it as done or skipped. Optional extras: pearl reviews and Anki (Me Deck and MGH only).
- **Sessions**: the department's weekly teaching activities, with resident sessions (grand rounds, morning report, core curriculum, journal club) shown on Today and optional division teaching folded away.
- **Schedule**: next 14 days and all 26 blocks of AY 2026-27. Tap a day to mark it on call, post-call, off, or pulled in.
- **Study**: questions this week against the weekly target, the last 8 weeks, AMBOSS progress, history, and a JSON backup.
- **Pearls**: one-line clinical pearls with a patient-information check, reviewed at 3 days and 2 weeks, then retired. Detailed notes stay in OneNote.

## Rules the app follows

- No identifiable patient information, ever. Capture warns on names, long numbers, dates, and bed numbers.
- No sync with AMBOSS. AMBOSS has no personal-data API, so questions are logged by hand in one tap.
- Missed days are dropped, never carried over. On call and post-call days need nothing. Rough or pulled-in days drop to the minimum (10).
- $0 to run.

## How it is built

- `app/index.html` is the whole app: one HTML page, plain JavaScript, no build step. It is published as a claude.ai Artifact and stores its data in the Artifact's database (`db` capability).
- `data/seed.json` is the data loaded at launch: rotation blocks, the first three weeks of assigned topics, day overrides, and settings.
- Claude updates the plan, AMBOSS numbers, and call days in the database directly, and copies a backup to `data/backup/` each Sunday.

### Database collections

| Collection | Document id | Fields |
|---|---|---|
| `blocks` | block id (`6A`) | `start`, `end`, `code`, optional `label`, `hours`, `slot` |
| `plan` | date (`2026-10-25`) | `topic` (an ABIM study plan module name), `filter` (`Module`; older rows `Systems` or `Disciplines`), `count`, `kind` (`study`, `review`, `rest`), optional `slot`, `note`, `optional` |
| `log` | date | `status` (`done`, `skipped`), `questions`, `pct`, `topic`, `at` |
| `days` | date | `type` (`auto`, `normal`, `call`, `postcall`, `off`, `pulled`), `rough` |
| `extras` | date | `anki` |
| `captures` | auto id | `text`, `area`, `link`, `created`, `stage`, `nextReview`, `status` (`active`, `retired`) |
| `sessions` | date and slug (`2026-10-27-grand-rounds`) | `date`, `start`, `end` (24 h `HH:MM`), `title`, `topic`, `place`, `kind` (`core` for resident sessions, `optional` for division teaching), `source` |
| `settings` | `main` | `weeklyTarget`, `minDay`, `examLabel`, `examMonth`, `amboss` {`answered`, `total`, `pct`, `epc`, `percentile`, `asOf`} |

## Changing the app

Edit `app/index.html`, check it locally, then republish it to the same Artifact URL so the data stays attached.
