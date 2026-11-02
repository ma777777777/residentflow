# ResidentFlow

A personal residency planner for one PGY2 internal medicine resident. It answers one question every day: what is my one study job today, given the rotation I am on?

Live app (private, sign-in required): https://claude.ai/artifact/1fRAEuyTcWiVgtevZq6jYr

## What phase 1 does

- **Today**: rotation, day type (night shift, on call, post-call, off, pulled in), and one study job: N questions in an assigned module of the AMBOSS Internal Medicine (ABIM) study plan, with a button that opens that module and a fixed time slot. One tap to log it as done or skipped. Optional extras: pearl reviews and Anki (Me Deck and MGH only).
- **Sessions**: the department's weekly teaching activities, with resident sessions (grand rounds, morning report, core curriculum, journal club) shown on Today and optional division teaching folded away.
- **Schedule**: next 14 days and all 26 blocks of AY 2026-27. Tap a day to mark it on call, post-call, off, or pulled in.
- **Study**: questions this week against the weekly target, the last 8 weeks, AMBOSS progress, history, and a JSON backup.
- **ECG track**: a bonus after the day's questions, never instead of them. An ECG card appears on Today once the questions are done (or on a day off), never on call, post-call, night, pulled-in or rough days. It works from his own downloaded copy of Medmastery ECG Mastery (OneDrive Desktop > medmastery): the Blue Belt (older 10-level version) as a quiz-first refresher, then the 68 Black Belt Workshop cases, 3 per session. It shows where the files are, links to matching LITFL practice ECGs, and logs the levels or cases finished, ECGs read and how many were right on the first read. The Study tab shows the phase, levels done, sessions this week against the goal (3), first-read accuracy, weak spots counted from ECG pearls ("Topic: what I missed"), and scored check-ups. Plan: https://claude.ai/artifact/2NLTka85DHiNeCY9NyVwVr
- **Learn**: one-line captures with a patient-information check. A *look-up* is a question to read about later (for example from a case on rounds): it waits with a Search AMBOSS button, shows on Today on lighter days (never on call, post-call or night days), asks "still want this?" after 2 weeks, and when done its one-line takeaway becomes a pearl. A *pearl* is something learned, reviewed at 3 days and 2 weeks, then retired. Detailed notes stay in OneNote.

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
| `extras` | date | `anki`, `ecg` {`lessons` (ids finished that day: `B4` for Blue Belt level 4, `K12` for Black Belt case 12), `lesson` (the first of them), `read`, `right`, `at`} |
| `captures` | auto id | `kind` (`lookup`, `pearl`; missing means `pearl`), `text`, `area`, `link`, `created`. Look-ups: `checkOn`, `status` (`open`, `done`, `dropped`). Pearls: `stage`, `nextReview`, `status` (`active`, `retired`), optional `q` (the look-up it came from) and `learnedOn` |
| `sessions` | date and slug (`2026-10-27-grand-rounds`) | `date`, `start`, `end` (24 h `HH:MM`), `title`, `topic`, `place`, `kind` (`core` for resident sessions, `optional` for division teaching), `source` |
| `settings` | `ecg` | ECG track: `start`, `owned` (`yellow`, `blue`, `black`), `ownedConfirmed`, `priorCompleted` (courses done before: quiz first), `localFolder`, `weeklyGoal`, `focus` (set by Claude each Sunday), `done` {level id: date}, `phases` [{`name`, `from`, `to`, `goal`}], `assessments` [{`date`, `label`, `right`, `total`, `dangerMissed`}], `planDoc` |
| `settings` | `main` | `weeklyTarget`, `minDay`, `examLabel`, `examMonth`, `amboss` {`answered`, `total`, `pct`, `epc`, `percentile`, `asOf` (date checked), `checkedAt` (UTC time checked; logs saved on that day after it are added on top)} |

## Changing the app

Edit `app/index.html`, check it locally, then republish it to the same Artifact URL so the data stays attached.
