# Draft: reply to Marcus Oyelaran, "who gets asked first" (not sent)

Reply to his follow-up question. Internal. No responders are named. Separate from `message-to-marcus.md` (the 4.2 findings) and `message-to-marcus-with-option-brief.md`; if you send either of those, you can fold this answer into it instead of sending it on its own.

**Subject: Re: did the 4.2 ranking change apply to responders who'd been turning jobs down?**

Hi Marcus,

Short answer: it applied to everyone, the same way, from the release on 12 August. It wasn't limited to new responders, and it wasn't aimed at people who had been turning jobs down.

**What the code says** (`dispatch-routing`, which I've read but not run)
- 4.2 changed shared settings in `config.py`: the proximity weight from 0.45 to 0.60, the recent-acceptance weight from 0.40 to 0.25, and the ping wait from 90s to 60s. Those values are used for every responder on every callout.
- There is no new-versus-existing rule. The only thing that differs by responder is their recent-acceptance score, which starts at a neutral 0.5 for a new responder and moves by the same amounts for everyone (+0.08 for a take, -0.12 for a turn-down or a miss).
- If anything, 4.2 made a history of turn-downs count for less in the ordering, because the acceptance weight went down.

**What the ping data says**
If the change had been aimed at responders who turned jobs down, they should be the ones who lost pings. They aren't. Among the 16 responders, I compared each one's turn-down rate before 12 August with how their pings per week changed afterwards:
- The four who lost about two thirds or more of their pings had turn-down rates from the highest in the group down to the second-lowest, so earlier turn-downs don't separate them from everyone else.
- Two of the responders with the highest turn-down rates gained pings (about +16% and +32%).
- What separates the four is what happened after the release: a run of missed pings, then very few pings. That is the pattern I described in my earlier note.

**What I can't see**
- I've only read the routing code in that folder. The 4.0 changelog mentions a routing override audit log. If overrides can be set for a particular responder, that would sit outside this code and could be an exception I can't see.
- The ranking scores themselves aren't in the database, so I can't check the order Dispatch actually used for any callout.

**Two things that would help from you**
1. Can handlers or anyone on your team set a per-responder override on ranking, and did any apply to these responders around 12 August?
2. Was it the intent that the weight change applied to existing responders with history, or was something meant to be phased in for them?

Happy to go through the numbers with you and Wen Li whenever suits.

Thanks,
[Your name]
