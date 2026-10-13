# Draft: message to Wen Li, six questions (not sent)

**Subject: 4.2 routing: six questions before I form a view**

Hi Wen Li,

I'm the new PM on Dispatch, and I'm trying to understand 4.2 before Marcus and I regroup on it. You're the person who built the ranking, and there's no written description of it, so I'd value 20 minutes with you.

I pulled the ping data and read the routing code. Here's what I see:

- Acceptance held at about 77% until 12 August. It then fell to 54% and has partly recovered since. Missed pings rose from 1-3% of pings to 21%, and they're still at 13%.
- Four responders went from about 49 pings a week combined to 3, and none has taken a ping since 24 August. Meanwhile everyone else's volume went up.
- In `history.py`, a decline or a miss costs 0.12 and an accept earns 0.08. A score only moves when someone is pinged, and there's no decay (the 2019 TODO).
- 4.2 cut the acceptance weight from 0.40 to 0.25 and the ping wait from 90s to 60s.
- I replayed one of the four responders' pings through that scoring. Her score falls to zero by 28 August. Her first misses came while the score was still high, so the score doesn't explain why the misses started.

My guess is that missed pings from the shorter wait push people down the list, and then they rarely get pinged again, so they can't earn the score back. I can't see the ranking scores themselves, so this is inference.

Can you help me with these?
1. Does that match how you understand it, or am I missing something?
2. Marcus asked on 14 Aug whether the new weighting was meant to apply to responders who've been turning jobs down. Was that a decision, or did it fall out of the config?
3. The code counts a miss the same as a decline, but the Glossary says they're recorded separately. Was counting them the same intended?
4. Is there any way for you to pull the current recent-acceptance scores for those four responders (Vesper, Farlight, The Undertow, Meteor Mite)?
5. What is Availability Confidence? It's committed for 4.2 on the roadmap but I can't find it in the release or the code.
6. Can anything outside the app's availability toggle remove someone from the candidate list, and do you have a record of availability changes for the four?

I'm not proposing a change yet. I'd like to understand it with you first, then take it to Helen.

Thanks,
[Your name]
