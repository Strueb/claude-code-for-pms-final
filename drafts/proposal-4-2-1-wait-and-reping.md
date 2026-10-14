# Proposal: two updates to the 4.2 routing change (draft, not sent)

**For:** Helen Achebe, Marcus Oyelaran (engineering sizing), Wen Li (how routing works). **From:** [Your name]. **Status:** draft. Wen Li has not confirmed the cause below, so this is a proposal to test, not a validated fix. Internal only: it describes ranking behaviour and refers to responders in groups, not by name.

## The ask

Ship a small follow-up to 4.2 with two changes to `dispatch-routing`:

1. **Put the ping wait back to 90 seconds** (it was cut from 90 to 60 in 4.2).
2. **Re-ping a responder once before treating the callout as a miss.** Today a ping that gets no answer in the wait is recorded as missed straight away, and costs the responder 0.12 of their recent-acceptance score. With this change, a first no-answer triggers one more ping to the same responder, and only a second no-answer counts as a miss.

## Why

- **Missed pings jumped on the release day.** They ran at 0–1 a day before 12 Aug and were 7 on 12 Aug and 12 on 13 Aug. Over the weeks, about 2% of pings were missed before 4.2 and about 12% for the steady responders since (18% overall), and the 4.2 setting is confirmed live in the data (gap before the next ping after a miss: 91–93 s before 12 Aug, 61–63 s after).
- **Fifteen tickets say the ping moved on before the responder could answer** (from 12 Aug, e.g. "phone buzzed, by the time I unlocked it, gone").
- **Misses cost the same as a turn-down, and there is no decay.** Four responders went from about 49 pings a week combined to 3, and their replayed scores sit near 0. A score only changes when someone is pinged, so they have no way back.
- **Most misses are recoverable today but cost time.** Of 135 missed pings, the next responder took 90 (67%), turned down 9, missed 10, and nobody was left in 26 (those callouts went unfilled).
- **It isn't seasonal volume.** Callouts fell about 15% (20.0 to 16.9 a day), but the break is a one-day step on 12 Aug, and unfilled callouts rose from about 5.5% to 11% when fewer callouts should make them easier to fill.

## What would change

| | Today (4.2) | After this proposal |
|---|---|---|
| Ping wait | 60 seconds | **90 seconds** (`OFFER_TIMEOUT_SECONDS` in `config.py`: one value) |
| First no-answer | Offer withdrawn, recorded as MISSED, score −0.12 | **Ping the same responder once more**, then check the three outcomes again |
| Second no-answer | – | Offer withdrawn, recorded as MISSED, score −0.12 (as today) |
| Taken / turned down | Unchanged | Unchanged (+0.08 and −0.12) |
| Ranking weights | 0.60 / 0.25 / 0.15 | Unchanged |

The retry is a change in `offer.py` (`offer_to`): re-send once before returning MISSED. See `Module 3 and 4/dispatch-routing-flow.html` for the flow with the proposed step drawn in.

## One design choice to settle: what does "re-ping" mean?

This decides how much extra time a callout waits, so it needs Wen Li and Marcus.

| Version | What happens | Longest wait per responder |
|---|---|---|
| **A. Second full window** | After the first wait, send a fresh ping and wait the full time again | 180 s (90 + 90) |
| **B. Nudge inside the window** | Send the same offer again partway through the 90 seconds (for example at 45 s); the window does not get longer | 90 s |

If everyone at the top of a five-person list missed, the callout would wait 300 s today, 450 s at 90 s alone, 900 s with version A, and 450 s with version B. About 1 in 7 pings is missed, and a callout averages about 1.3 pings, so most callouts would not notice either version, but the slow ones would be slower under A. **I'd propose B**, unless engineering says a nudge isn't possible, and I'd start with it only on the last responder on the list if sizing is a concern.

## What I expect, and what I don't know

**Expected (inference):**
- Missed pings for the steady responders fall from about 12% toward their pre-4.2 level of 2–3%.
- The "gone before I could answer" tickets taper, and unfilled callouts fall back toward 5%.

**Not known, and why it matters:**
- **Whether people answer between 60 and 90 seconds.** Every turned-down ping, before and after 4.2, was answered within 40 seconds (median 22 s). Taken pings can't be timed, so the data can't show whether the late answers exist. If takes are as fast as turn-downs, a longer wait recovers little.
- **Why pings are missed.** Push never arrived, phone off, away, or too slow are all recorded as "missed". A re-ping helps with the first only if the first push failed to arrive.
- **What a re-ping does on the phone** (a second buzz, a duplicate card, a reset timer). Needs Wen Li.

## What this proposal does NOT do

- **It does not bring back the four responders who have gone quiet.** Their scores are near 0 and they get roughly one ping a week. Fewer misses means fewer pings cascade to the bottom of the list, so they could get even fewer. For them we'd still need option B (a miss costs less than a turn-down), C (decay) or D (a one-off reset) from the option brief. A re-ping before a miss means fewer misses are recorded, which helps scores, but doesn't reverse a score already at 0.
- **It does not change how a miss is scored.** That is a separate decision for Wen Li and Helen.

## Reporting note for Ravi

A re-ping changes what a "ping" and a "miss" mean in the data: pings may be counted once or twice, and a miss would be recorded only after the second try. The weekly numbers before and after the change would not be directly comparable unless the definition is agreed first. Needs a decision before it ships.

## How we'd know it worked (4 weeks after release)

| Measure | Now | Target |
|---|---|---|
| Missed pings, steady responders | about 12% | 3% or less |
| Overall acceptance (weekly) | about 73% | about 77% |
| Unfilled callouts (weekly) | 13–14% in the worst weeks, 5.5% in the last full week | stays below 6% |
| "Gone before I could answer" tickets | 2–3 a week | near zero |
| Pings to the four quiet responders | about 1 a week combined | rising (if not, scoring is the cause) |

Check the first two after the wait is restored and again after the re-ping, so we can tell which change did what.

## Rollout, so each change can be judged

1. **4.2.1: restore 90 seconds.** One config value, easy to reverse.
2. **Then the re-ping,** first on the last responder on the list only (where a miss now ends the callout), then widened if it helps.
3. **In parallel, decide on the scoring change** so the four can recover.

## Risks

- **Slower callouts** (see the table above), mainly with version A. Version B limits this.
- **The 60-second wait may have been chosen for a reason** I don't know. Ask Wen Li and Marcus before reverting; Priya advised against a revert debate without confirming first.
- **A re-ping could annoy responders** if it buzzes twice. Sofia can say whether the phone app handles that cleanly.
- **Everything above is inferred from ping data.** The live scores, taken-ping timings and prior-year data are not in the database.

## What I need

1. **Wen Li:** confirm the mechanism, why 60 seconds was chosen, how long accepting a ping usually takes, and what a re-ping would do on the phone.
2. **Marcus:** sizing, the next release slot, and whether a nudge within the window (version B) is feasible.
3. **Helen:** a decision on the two updates and on the scoring change, and who else must approve a routing change.
4. **Ravi:** prior-year numbers, and agreement on how the weekly reporting treats a re-ping.
