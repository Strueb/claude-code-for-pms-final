# How Dispatch decides who gets pinged

**Status: draft, internal.** Written from the `dispatch-routing` code (`00-rook/code/dispatch-routing/`) as it stands after 4.2 (12 Aug 2026). Not yet checked by Wen Li, who built it, so treat anything marked *to confirm* as unverified. Priya Raghunathan asked for this to be written; nothing like it existed before.

## The short version

When a callout comes in, Dispatch makes a list of everyone who is available in that area, puts them in order, and pings them one at a time, best match first. Each responder gets a short window to answer (60 seconds since 4.2, 90 before). If they take it, we stop. If they turn it down, or don't answer in time, we move to the next person. Nobody is left off the list because of how they have answered before. Their history only affects where they sit in the order.

## Step by step

1. **A callout arrives** for a region at a time.
2. **Dispatch builds the list.** Everyone marked available in that region at that moment. Availability is set by the responder or their handler in the app. Nothing in routing changes it.
3. **Dispatch ranks the list.** Each responder gets a score (see below) and the list is sorted from highest to lowest. Nobody is removed here.
4. **The top responder is pinged.** The callout goes to the phone they are signed in on, and Dispatch waits up to the *ping wait*.
5. **Three things can happen:**
   - **Taken:** they say yes. The callout is filled and the list stops.
   - **Turned down:** they say no. We move to the next responder.
   - **Missed:** no answer before the ping wait ran out. The offer is taken off their phone and we move to the next responder.
6. **Repeat** down the list until someone takes it. If we reach the bottom with no yes, the callout goes **unfilled**.
7. **After each answer, Dispatch updates that responder's recent-acceptance score** (see "The acceptance score").

## How the ranking score is worked out

Each responder's score is three parts added together, each multiplied by a weight. Higher is better.

| Part | What it measures | Weight now (4.2) | Weight before 4.2 |
|---|---|---|---|
| **Proximity** | Estimated *travel time* to the callout, not straight-line distance (since 4.1). 1.0 if they are practically there, falling to 0 at 45 minutes or more | **0.60** | 0.45 |
| **Recent acceptance** | The running score described below, between 0 and 1 | **0.25** | 0.40 |
| **Capability match** | Share of the capability tags the callout needs that this responder has. 1.0 if they have all of them (or none are needed), 0 if none | 0.15 | 0.15 |

The weights are relative to each other and don't need to add up to anything.

**Worked example.** Two responders are the same travel time away (10 minutes) and both have every capability the callout needs. Proximity is 1 − 10/45 ≈ 0.78 for both. One has a recent-acceptance score of 0.9 and the other 0.

| | Proximity part | Acceptance part | Capability part | Total |
|---|---|---|---|---|
| Score 0.9 | 0.60 × 0.78 = 0.47 | 0.25 × 0.9 = 0.23 | 0.15 | **0.84** |
| Score 0 | 0.47 | 0 | 0.15 | **0.62** |

The gap is 0.225. Before 4.2 the same two would have been 0.36 apart. In travel-time terms, a responder with a score of 0.9 is worth about 17 minutes of extra distance now (it was about 36 before). The 4.2 change therefore made the acceptance score matter less in the ordering, and made distance matter more.

## The acceptance score

- Every responder has one, between **0 and 1**, starting at **0.5** when first taken on.
- **A taken callout adds 0.08.**
- **A turned-down callout takes off 0.12. So does a missed one.** The code treats the two identically ("we asked and we didn't get a yes"), although the glossary says missed is recorded separately.
- **It only changes when the responder is pinged.** A responder who is not pinged cannot earn points back.
- **It never decays.** A 2019 comment in `history.py` asks whether the score should drift back towards 0.5 over time, with arguments on both sides, and was left as is.

**What that means in practice.** With a take rate of about 77%, a responder gains a little on average (+0.034 per ping) and sits near the top of the range. One miss costs 0.12, which takes one and a half taken callouts to win back. A responder at 0 needs about seven taken callouts in a row just to reach 0.5. If their low score keeps them from being pinged, they get no chance to do that.

## What is *not* in the ranking

- **Who the responder is.** Nothing uses identity, cover identity or any personal detail (Security Policy 4.1 forbids us from storing or mapping it).
- **Time of day.** The code does not use it.
- **How many callouts a responder already took today.** The code does not count workload.
- **Availability as a ranking input.** The glossary lists it, but in the code it is a filter: only people marked available are on the list at all. *To confirm with Wen Li.*

## What changed in 4.2

| Setting | Before | Now |
|---|---|---|
| Ping wait | 90 seconds | **60 seconds** |
| Proximity weight | 0.45 | **0.60** |
| Recent-acceptance weight | 0.40 | **0.25** |
| Capability weight | 0.15 | 0.15 |

The release notes mention the weights and the wait. They do not mention that a shorter wait means more missed pings, and that each miss lowers a responder's score.

## Where the glossary and the code differ

- **Missed vs turned down.** Glossary: recorded separately. Code: scored the same.
- **Availability.** Glossary: a routing input. Code: a filter before ranking.
- **Who writes the Availability Record.** Glossary: Dispatch writes it. `availability.py`: routing never changes it, and responders or handlers set it in the app; Supply only reads it.

## What the data shows, kept separate from the code

These are findings from the ping data, not statements about the design.

- Ping wait: gaps between a missed ping and the next ping were 91–93 seconds before 12 Aug and 61–63 seconds after, so the 60-second setting is live.
- Four responders fell from about 49 pings a week to 3 after 12 Aug. Replaying their pings through the scoring rules puts their scores near 0 (an inference, since scores are not in the database).
- The other responders' scores dipped in the first week after the release and recovered.

## Open questions *(to confirm with Wen Li)*

1. Was it intended that a miss counts the same as a turn-down?
2. Why was the score never given a decay, and should it have one now?
3. Why was the ping wait set to 60 seconds, and what does "how long does it take to accept a ping" look like in practice?
4. Does anything outside the code (phone app, push delivery, capability tags) change the order?
5. How does a new responder, or one returning from time away, start?
6. What happens when nobody is available, or when everyone on the list misses?

## For support: answering "why wasn't I pinged?"

- Everyone who is available in the area is on the list. Nobody is left off.
- The order depends mainly on travel time, then on how often the responder has recently taken callouts, then on skills.
- A responder lower down is asked only after the people above them have turned the callout down or not answered in time. If those above take it, the responder further down is never pinged.
- Please don't say anything about *why* a particular responder sits where they do until the cause of the 4.2 changes has been confirmed.
