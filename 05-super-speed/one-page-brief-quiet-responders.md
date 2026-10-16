# Bringing quiet responders back: one-page brief

DRAFT for Helen Achebe · Dispatch PM · not yet checked with Wen Li or Marcus Oyelaran

## The problem in one line
A responder who misses or turns down pings drops down the list. A lower place means fewer pings, and a responder who isn't pinged can't take one to climb back. Today only being asked brings someone back, and being quiet is what stops them being asked.

**What the data shows (database to 7 Sep).** Four responders went from about 49 pings a week combined to 3 by 31 Aug. None has taken a ping since 19 Aug. Their misses started the week of 12 Aug. The other 12 absorbed the load and recovered to about 73% acceptance.
**What is inferred (not confirmed).** The code scores a decline or a miss at -0.12 and an accept at +0.08, with no decay. A score only moves when someone is pinged. Ranking scores aren't in the database, so Wen Li's answer settles it.

## Who it is for
1. **The handler of a quiet responder** (a handler like Kip). They see "nothing is coming" and today can only file a ticket. About 30 tickets say the phone never goes off.
2. **The responder who has gone quiet.** They are available and willing, but nobody asks them.

## What changes for them
- **Handler:** the console shows when a responder hasn't been offered a callout in a set number of days, and says so in plain words instead of leaving the handler to guess. One action: "Confirm availability and phone." No ticket needed.
- **Responder:** a quiet responder is offered the next suitable callout once their history has gone stale. A decline or a miss counts for less as it gets older, instead of counting forever. One taken ping puts them back in the normal order.
- **Retry on a missed ping:** if nobody answers, the same responder gets one more nudge on their phone before the ping is recorded as missed and moves on. The nudge sits inside the same wait, so a callout still moves on in the same total time. If they answer the nudge, it counts as taken and costs nothing. Only a ping that stays unanswered after the nudge counts as a miss.
- **Everyone else:** nothing visible. Callouts still go to the best match first.

## What it deliberately does not do
- It does not revert 4.2 or the proximity weighting. Responders asked for that change.
- It does not guarantee anyone a number of pings, or put a weak match ahead of a clearly better one on a critical callout.
- It does not change availability by itself. Handlers stay in control of it.
- The retry is one nudge, not repeated pings. It does not nag, and it does not retry after a turn-down.
- It does not tell us why a ping was missed. A failed push, a phone problem or a slow thumb all look the same in the data.
- It does not touch cover identities. Nothing links a responder to a legal identity (Security Policy 4.1).
- It does not cover mutual aid (Q4 exploration) or the handler phone app.

## Open before anyone builds
- **Wen Li:** was treating every responder the same on purpose? Why no decay (2019 TODO)? Should a miss score like a decline?
- **Ravi Menon:** definition of a "ping" and a "miss" if we add a re-ping; prior-year numbers.
- **Marcus Oyelaran:** sizing, and whether scores survive a restart.
- **Retry:** a nudge inside the wait (stays at 90s) or a second full window (up to 180s per responder)? I propose the nudge. It also changes what "ping" and "miss" mean in the data, so Ravi must agree the definition first. It can only help if the miss was a missed or unseen alert; we can't tell yet whether a failed push is distinguishable from "no answer". Of 135 missed pings, 26 ended with nobody left to ask.
- **Separate and small:** restoring the 90s wait is a config change. Alone it probably won't bring the four back.

## How we'd know it worked
The four responders are offered pings again within a week, and the share of pings missed falls. Callouts with nobody left to ask fall from 11% toward 5%. Handler tickets saying "quiet" fall.
