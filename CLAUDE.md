# Notes for Claude

- The app is `app/index.html`, published as the Artifact https://claude.ai/artifact/1fRAEuyTcWiVgtevZq6jYr with capabilities `db` and `downloads`. Republish to that URL; a new URL would lose the data.
- Data lives in the Artifact database, not in this repo. Read and write it with the ArtifactData tool. `data/seed.json` is only the launch data; `data/backup/` holds weekly exports.
- Never store identifiable patient information anywhere: not in the database, the repo, or commit messages.
- Do not build a note-taking system. Pearls are one line; detailed notes live in the user's OneNote.
- Do not add AMBOSS scraping or automatic sync. AMBOSS has no personal-data API.
- Keep it $0: no paid services, no paid APIs.
- Plain JavaScript, no build step, no dependencies. Dates are local `YYYY-MM-DD` strings; weeks run Sunday to Saturday.
- Weekly plan rows: shuffled specialties, weighted toward cardiology and the current or next rotation. Store the exact AMBOSS filter label in `topic` and `filter`; the app shows the specialty name (map in `SPECIALTY`). In AMBOSS ABIM, organ specialties are Systems (cardiology = `Cardiovascular System`, nephrology = `Renal & Urinary Systems`, rheumatology = `Musculoskeletal System`, and so on); there is no Cardiology or Nephrology discipline. Use the Disciplines filter only for `Infectious diseases`, `Oncology`, `Critical care` and `Aging` (geriatrics).
