# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (took over from Priya Raghunathan, who left 21 Aug 2026). Sources: rook-wiki (About Rook, Glossary, Team directory, Releases, Q3 roadmap, Product briefs), `00-rook/company/notes/handoff-from-priya.docx`, and the 4.2 release-page comments. Rook data is also in the rook-database connector.

### Company and products
- Rook Industries sells coordination and provisioning software to independent masked responders and their handlers/quartermasters. HQ Site Aleph; offices in Berlin, Singapore, Cornwall. Per-active-responder subscription. Monthly release train, 4.x point releases.
- **Rook Dispatch** (mine, flagship): ranks available responders for an incident, pings the top one's phone, they take it or not, else it moves on. Handlers use the web console; responders use the phone app. Routing config ships in the release, not as a runtime setting.
- **Rook Supply**: gear requisitions, maintenance, field failure reports. Users are handlers and quartermasters.
- Confidentiality is contractual: we never store or map responder cover identities to legal identities (Security Policy 4.1). Never design anything that assumes we can, and never try to work out who anyone is.

### People (role names on wiki comments → person)
- Engineering manager → **Marcus Oyelaran**, Eng Manager, Dispatch (Site Aleph). Straight talker; Priya's first stop.
- Staff engineer → **Wen Li**, Berlin. Built the "who gets pinged" logic; no written doc exists, so ask her.
- Product designer → **Sofia Marino**, Site Aleph. Owns console and phone app; ran the September interviews.
- Support lead → **Nadia Hoffmann**, Berlin. Owns tickets; hears handler complaints first.
- **Helen Achebe**, Director of Product, my boss. Owns roadmap and commitments.
- **Ravi Menon**, Data Analyst, Singapore. Owns the weekly acceptance reporting. (Priya's handoff says the eng manager pulls numbers; the directory says Ravi. Go to Ravi for real numbers.)
- Halloran: handler (Sgt. Bulwark) who also acts as the Supply quartermaster contact in the briefs; not in the directory.

### Vocabulary
- **Callout**: request for a responder to attend an incident. **Ping**: a callout offered to one responder's phone. **Taken / turned down / missed** (missed = no answer before the ping wait ran out; recorded separately from turned down).
- **Ping wait**: how long a ping sits before moving on (90s → 60s in 4.2).
- **Acceptance rate**: share of pings taken. Headline metric. **Time-to-accept**: median seconds from ping to taken. **Coverage gap**: nobody with the needed capability tags was available, which is different from a low acceptance rate.
- **Routing priority**: ranking score from proximity (travel-time estimate), availability, capability match, and recent acceptance history. Turning down or missing pings lowers a responder's place in later callouts.
- **Capability tag**, **Responder Availability Record** (written by Dispatch, read by Supply), **Mutual aid** (not supported; Q4 exploration), **Requisition**, **Field failure report**, **Service interval**.

### Release history
4.0 (7 Apr): console nav, responder profile redesign, routing override audit log. 4.1 (16 Jun): travel-time proximity, bulk callout, push reliability. 4.2 (12 Aug): proximity weighted up vs. acceptance history, ping wait 90→60s, persistent console filters, three defect fixes.

### Where things stand
- **4.2 is the live problem.** Database (29 Jun to 6/7 Sep): acceptance was steady ~77% until 12 Aug, then 54/66/67/73% by week. Missed pings went from 1-3% to 21%, then 18/15/13%. Tickets went from 6-8 a week to 20-32. Ping volume held at ~170 a week. Support's themes: "phone never goes off" (~2/3) and "gone before I could answer" (~1/3, explained by the 60s wait).
- **Not seasonal on this evidence.** Callout volume dipped ~10%, but acceptance is a ratio and the drop is concentrated: four responders (Vesper/Aunt Dot, Farlight, The Undertow, Meteor Mite/Kip) went from ~49 pings a week combined to 3 by 31 Aug, and none has taken a ping since 24 Aug; the other 12 absorbed the load. The overall "recovery" partly reflects those four dropping out. Unfilled callouts rose from 5.5% to 11%. No prior-year data exists, so seasonality is not ruled out, only shown not to explain this pattern. Priya and Marcus's seasonal read was never backed by data.
- **Likely mechanism (inference; ranking scores aren't in the database):** `history.py` scores a decline or a miss at -0.12 and an accept at +0.08, with no decay (2019 TODO), and a score only moves when someone is pinged. 4.2 cut the acceptance weight 0.40→0.25 and the ping wait to 60s, so misses push someone down the list and they never get a chance to recover. Code treats all responders alike, which answers Marcus's 14 Aug config question; whether that was intended is for Wen Li. Priya advised against a revert debate; decide with Helen only after confirming with Wen Li.
- **Roadmap is stale** (last reviewed 30 Jun, all owned by Priya). Committed for 4.2: routing change (shipped), ping timeout tuning (shipped), **Availability Confidence (not in the 4.2 notes, so apparently slipped)**. Committed for 4.3: Requisition approval chains (Supply). Q4 exploring: Handler phone app, Shared cover between responders. Priya says to ask Helen which squeezed-out items are still Q3 commitments; that talk hasn't happened.
- Briefs: Bulk Callout and Routing Override Audit Log already shipped (4.1, 4.0) though the wiki briefs still read as proposals. Handler Phone App (8 Sep, Sofia, exploring, no baseline metric) is for handlers, not the existing responder app. Requisition Approval Chains (Supply) starts as one second-approval step over $2,000 and balloons into a dashboard, bypass and spreadsheet replacement, so scope needs cutting.
- No written description of how pinging is decided. Priya asked me to write it. Console filter-persistence tickets are noise; don't spend time on them.

### How to help me
Check claims against the data before repeating them. Keep facts (wiki, database) separate from opinions (Priya's read). Flag when roles or sources conflict.

- Unsent drafts and the full contradictions/data write-up are in `drafts/` (Wen Li, Helen, Ravi, `dispatch-4-2-analysis.md`). Plan: Wen Li first, Helen after the reply, Ravi for per-responder and prior-year numbers. Keep responder names out of wide channels.
- Database has five tables (callouts, pings, responders, handlers, support_tickets), data only to 6/7 Sep, with no ranking scores or prior-year history.
- Handlers of the four frozen-out responders: Aunt Dot (Vesper) and Kip (Meteor Mite) filed no tickets, so call them; Linda Pruitt (Farlight) and Desmond Okafor (The Undertow) filed many.
- Still open: Wen Li's intent on equal treatment of all responders, what Availability Confidence is, who owns the Supply 4.3 commitment, and whether a miss should score like a decline.
- More unsent drafts now in `drafts/`: Aunt Dot, Kip, Marcus, Nadia. Nothing has been sent yet; Marcus and Nadia are internal, the two handlers are customers (keep the cause out of those messages).
- Decision: Helen first, before anyone above her. Helen's boss isn't named anywhere in the wiki, so ask for the name only if it comes to that.
- Drafts added since: Linda Pruitt, Desmond Okafor (send the same day with the same promised date), Sofia, Meteor Mite and The Gale (via Kip first; the channel for reaching a responder is unconfirmed), and a Wen Li ranking walkthrough for the pinging write-up Priya asked for. All unsent, all in `drafts/`.
- The Gale (Kip's other responder) is not frozen out: pings rose from about 13 to 17 a week with more misses and declines, so the load shifted to the other 12 responders may become a problem too.
- Sofia ran the September interviews but `feedback/` is empty, so her notes still need to be requested. Desmond also has an unrelated open billing ticket (invoice lists The Undertow twice); ask Nadia.
- Four handler interviews (Aunt Dot, Ambrose, Halloran, Kip; 2-5 Sep) are summarized in `feedback/handler-interviews-summary.md`, with a chart in PNG and HTML. Callouts gone before the responder can answer (3 of 4) and console alerts that are hard to notice or tell apart (3 of 4) were the most common complaints. Text too small and uneven workload were each raised by 2 of 4.
- The interviews describe symptoms only; no handler explains the cause. They don't confirm 4.2 as the trigger, since only Halloran gave a date ("sometime in August").
- Single mentions: dark mode (Kip), silent filter reset and buried tag legend (Ambrose), and three Supply problems from Halloran (one requisition queue, failure reports with no feedback, weak catalog search).
- In the Wen Li draft, the data and code facts are marked [CONFIRMED] and the missed-ping feedback loop is marked [INFERENCE, not confirmed]. Her answers to questions 1 and 4 (current scores for the four responders) would settle it. Remove the markers before sending.
