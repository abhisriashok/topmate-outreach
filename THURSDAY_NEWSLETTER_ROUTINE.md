# Thursday Newsletter Push — Routine Definition

A fourth scheduled routine (alongside the Monday/Wednesday/Friday outreach routines),
firing every **Thursday around 11:00 AM IST**. It announces Abhineet's latest LinkedIn
newsletter post to everyone already in the outreach system and invites them to subscribe.
It does **not** touch the Tracker's Stage/Next Action columns or the 5-touch outreach
sequence — this is a separate, parallel broadcast.

## How the article gets in

Abhineet edits `newsletter_queue.md` (repo root) before the routine fires each week,
filling in `Title`, `Link`, and optionally a one-sentence `Secondary hook`. The routine
never fires without a human first queueing a link — there is no auto-discovery.

## What the routine does, each Thursday

1. Read `newsletter_queue.md`. If `Title`/`Link` are blank, or `Last sent` already
   matches this week's date, **send nothing** — just report "no new article queued
   this week" and stop. Never resend the same article twice, never invent a link.
2. Read `tracker.xlsx`'s "Tracker" sheet. Build the send list: every row with a valid
   Email, excluding the row 2 example ("Jane Doe"), and excluding anyone who is
   `Bounced? = Y`, `Opted Out? = Y`, or `Booked? = Y` (same permanent-exclusion rule
   used everywhere else in this repo).
3. Build the email from the "Newsletter Announcement" template (`email_templates.md`
   plain-text body + matching block in `email_templates.html`), substituting:
   - the article title and link from the queue file (replace the hardcoded
     "Demo went perfectly, deal stalled anyway" title/link with this week's)
   - the fixed subscribe link (always the same):
     `https://www.linkedin.com/build-relation/newsletter-follow?entityUrn=7475477920728170496`
   - `[FIRST_NAME]` from each contact's Name, `[YOUR_NAME]` = Abhineet
   - the optional `Secondary hook` line if provided, otherwise the default opener
4. Rotate subject lines across the batch (don't send one identical subject to
   everyone — vary it the same way the Monday routine's Email 1 does).
5. Send via the Gmail connector's `send_message` tool — a real send, not a draft.
   This is a broadcast Abhineet has explicitly authorized to run unattended, same as
   the Mon/Wed/Fri sequence.
6. Append one line to each sent contact's Notes column: e.g.
   `| Newsletter announcement sent <date> ("<title>")`. Do not change Stage or
   Next Action.
7. Update `newsletter_queue.md`: set `Last sent` to today's date, and clear
   `Title`/`Link`/`Secondary hook` back to blank so next week starts empty.
8. Commit `tracker.xlsx` and `newsletter_queue.md` together, push.
9. Report a short digest: article sent, total recipients, breakdown of exclusions
   (bounced / opted-out / booked / no-email), and any bounces/opt-outs that come back
   from *this* send should be picked up by the next run of any routine's Gmail scan.

## Exclusions — same rule as the rest of the repo

`Bounced? = Y`, `Opted Out? = Y`, or `Booked? = Y` in `tracker.xlsx` → never emailed
by this routine either. This is checked fresh against the Tracker's current state
every Thursday, not against a cached list — someone who unsubscribed on Tuesday must
never get Thursday's newsletter push.

## Follow-up touches (optional, per-article)

If Abhineet asks for a 3-touch push on a given article (initial + nudge + final,
using the "Newsletter Announcement — Follow-up 1/2" templates in
`email_templates.md`/`.html`), those are one-off asks handled per-article in a live
session — they are not part of this recurring Thursday definition.

## Setting up the trigger

This file is meant to be pasted (or referenced) as the scheduled prompt when
configuring the Thursday trigger in Claude Code on the web's scheduling settings —
the same place the Monday/Wednesday/Friday routines are configured. See
https://code.claude.com/docs/en/claude-code-on-the-web for how triggers work in this
environment.
