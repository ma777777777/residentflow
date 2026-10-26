# Notes for Claude

- The app is `app/index.html`, published as the Artifact https://claude.ai/artifact/1fRAEuyTcWiVgtevZq6jYr with capabilities `db` and `downloads`. Republish to that URL; a new URL would lose the data.
- Data lives in the Artifact database, not in this repo. Read and write it with the ArtifactData tool. `data/seed.json` is only the launch data; `data/backup/` holds weekly exports.
- Never store identifiable patient information anywhere: not in the database, the repo, or commit messages.
- Do not build a note-taking system. Pearls are one line; detailed notes live in the user's OneNote.
- Do not add AMBOSS scraping or automatic sync. AMBOSS has no personal-data API.
- Keep it $0: no paid services, no paid APIs.
- Plain JavaScript, no build step, no dependencies. Dates are local `YYYY-MM-DD` strings; weeks run Sunday to Saturday.
- Weekly plan rows: study days are modules of the AMBOSS "Internal Medicine (ABIM)" study plan, not custom sessions. Set `filter: 'Module'` and `topic` to the exact module name, a key of `MODULES` in the app; the app shows the specialty name and its AMBOSS button opens that module, where he taps Resume. Shuffle modules, weighted toward cardiology, the current or next rotation and weak modules (by percent correct), and bring in the untouched modules (psychiatry, dermatology, geriatrics and so on) over the year. Module progress counts only questions answered inside the module, so never assign a custom session for a specialty. Saturday reviews stay `kind: 'review'` custom sessions of marked questions. Older rows use `Systems` or `Disciplines` labels (map in `SPECIALTY`).
- Sessions come from the weekly email "Department of Internal Medicine Weekly Activities" (from intmed@aub.edu.lb, sent Fridays) in the user's Outlook. Resident sessions are `core`; division core curriculum, ECG and board review sessions are `optional`; skip tumor boards, fellow journal clubs and student report. Store only title, topic, time and place: no presenter names, and never patient details.
