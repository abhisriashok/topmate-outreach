# manual_sourcing/

Fallback pipeline for weeks when the connected Apollo.io account's prospecting/search API
is plan-gated (trial plans can show available lead credits while still returning
`API_INACCESSIBLE` on `apollo_agent_find_prospects` / `apollo_mixed_people_api_search` /
`apollo_mixed_companies_search` — see tracker.xlsx "How To Use" item 8, and the
2026-08-31 run for the first occurrence).

## How it works

1. Abhineet browses Apollo.io's own web app (unaffected by the API-level restriction),
   exports candidates to a spreadsheet with exactly these 6 column headers: `Name`,
   `Email`, `LinkedIn URL`, `Title`, `Company`, `Country`.
2. That file is committed here, named `<target-monday-date>_<Segment/Sub-group>.xlsx`
   (e.g. `2026-09-07_SegmentA1_Presales.xlsx`).
3. At the start of each Monday "Fresh List" run, check this folder for a file whose date
   prefix matches today. If one exists, treat every row in it exactly like an
   Apollo-returned candidate for step 5(f) of the routine — same exclusion checks (dedup
   against the live Tracker sheet, 3-country scope, correct segment/sub-group title),
   same Apollo Archive logging (Apollo Source = "Apollo.io (manual web UI — API blocked on
   trial plan)"), same Tracker fields, same live-send rules. Do NOT skip the re-verification
   in step 5(e) just because this file was pre-validated once — Tracker state can change
   between when the file was sourced and when it's actually processed.
4. After processing, move the file to `manual_sourcing/processed/` (same filename) and
   commit that move alongside the rest of the run's changes, so it's never picked up or
   re-sent on a future run.
5. If a file's date prefix doesn't match today (e.g. it's stale, or was queued for a
   different week), leave it alone — do not process it and do not delete it.
