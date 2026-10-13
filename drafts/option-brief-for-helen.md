# Option brief: Dispatch 4.2 routing (draft, not sent)

For: Helen Achebe. From: [Your name]. Status: draft. Wen Li hasn't replied yet, so the cause below is not confirmed. Send after her reply, or alongside the message in `message-to-helen.md`.

## The decision
Do we change Dispatch routing to fix the 4.2 problems, and which change? I'm not asking for a decision yet. I'd like your view on the options, and I'd like to take a proposal back once Wen Li has confirmed how the ranking works.

## What we know (from the database, code and tickets)
- Acceptance was steady at about 77% until 12 Aug, then fell to 54% and has partly recovered to 73%. Missed pings went from 1-3% to 21% and are now 13%. Unfilled callouts rose from 5.5% to 11%.
- Four responders went from about 49 pings a week combined to 3. None has taken a ping since 24 Aug. The other 12 took up the load, so the overall "recovery" partly reflects those four dropping out.
- Callouts in the four responders' areas fell by about the same as everywhere else (14-22%), so it isn't a lack of work there. Misses rose at every hour of the day, so it isn't a sleep or off-hours pattern.
- Support tickets: 45 of 147 are about the 4.2 problem (30 "phone never goes off" or very quiet, 15 "gone before I could answer"). Four handler interviews describe the same two symptoms.
- In the code, a decline or a miss costs a responder 0.12 of score and an accept earns 0.08. A miss and a decline count the same, there's no decay (an old TODO from 2019), and a score only changes when someone is pinged.
- 4.2 changed the ping wait from 90s to 60s, the acceptance weight from 0.40 to 0.25, and the proximity weight from 0.45 to 0.60.

## What we don't know
- **Cause (inference).** Misses from the shorter wait push responders down the list, so they're rarely pinged and can't earn the score back. A replay of one responder's pings fits this, but I can't see the real ranking scores. Wen Li can.
- **Why the misses started.** The first misses came when the replayed score was still high, so something other than the score caused them. The 60s wait is the most likely candidate, but a phone or push-delivery problem isn't ruled out.
- **Whether the ranking was meant to treat all responders alike**, and why the wait was cut to 60s.
- **Seasonality.** It isn't supported by this data, but there's no prior-year data to rule it out. Ravi may have it.

## Options

| Option | What it changes | What it should fix | Risks and unknowns |
|---|---|---|---|
| **A. Restore the 90s ping wait** | One config value, in the next release | The spike in misses, and the "gone before I could answer" complaints | I don't know why 60s was chosen. Callouts may take longer to reach the next responder. |
| **B. Count a miss as less than a decline** | Scoring in `history.py` / `offer.py`, in a release | Responders sinking because of misses they didn't choose | Whether a miss should count like a decline is a product decision for Wen Li and you. |
| **C. Let scores recover over time (decay)** | Scoring in `history.py`, in a release | Responders stuck low with no way back | The old TODO warns that someone who has stopped taking work would also recover. |
| **D. Reset the four responders' scores once** | A one-off operation | Immediate relief for the four | Treats the symptom; they could sink again under the current rules. Unknown how scores persist. |
| **E. Revert the acceptance weight to 0.40** | One config value | Not supported. | My replay suggests the 4.2 weighting made the score matter less in ranking, so reverting would likely make freeze-outs worse. |
| **F. Do nothing** | Nothing | Nothing | Acceptance may keep recovering as the other 12 absorb the load, but the four stay frozen out and the load on the 12 may become a problem. |

## My recommendation (provisional)
A first, to remove the spike in misses. Then B or C so a responder can recover, once Wen Li confirms how the scores work. D only as a stopgap for the four if the fix will take more than one release. I'd drop E and treat F as the risk to avoid.

## How we'd know it worked
- Missed pings back to about 1-3%. Acceptance back to about 77%.
- The four responders back to roughly 49 pings a week combined, and unfilled callouts back near 5.5%.
- Tickets about "phone never goes off" and "gone before I could answer" falling back toward the 6-8 a week we had before.

## What I need from you
1. Your view on A versus B/C, and whether the 60s change has a commitment behind it that I should know about.
2. Who else needs to approve a routing change. The config file says to tell the engineering manager before changing it.
3. Time to go through the Q3 commitments (the second item in my earlier message).

## Next steps
- Wen Li: confirm the mechanism and pull the four responders' current scores.
- Ravi: prior-year weekly numbers and the per-responder breakdown.
- Marcus: engineering view on release timing.
- Handlers of the four responders: customers, so contact should say nothing about the cause until it's confirmed.
