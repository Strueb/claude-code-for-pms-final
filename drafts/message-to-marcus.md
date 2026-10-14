# Draft: message to Marcus Oyelaran (not sent)

**Subject: 4.2: ready to regroup**

Hi Marcus,

Thanks for giving me room to look at 4.2 myself before we talked. I've now been through the ping data, the support tickets and the routing code, and I'd like to regroup this week.

**What I found**
- Acceptance was steady at about 77% until 12 August. It dropped to 54% the week of the release and was 73% in the latest week I can see. Missed pings went from 1-3% to 21%, and they're still at 13%.
- The drop is concentrated. Four responders went from about 49 pings a week combined to 3, and none has taken a ping since 19 August. The rest absorbed the load. That doesn't look like a seasonal dip to me, though I have no prior-year data to rule it out.
- On your question from 14 August: the code doesn't distinguish responders who have been turning jobs down. Everyone is ranked by the same weights (`config.py`), so it applies to them like anyone else. Whether that was a decision is for Wen Li.
- In `history.py`, a decline or a miss costs 0.12 and an accept earns 0.08. A miss is scored like a decline, and there's no decay. 4.2 also cut the acceptance weight from 0.40 to 0.25 and the ping wait from 90s to 60s. My working theory is that missed pings push responders down the list, and then they rarely get pinged again. I can't see the ranking scores, so this is inference.

**What I'm not proposing**
I'm not proposing a revert or a change to `config.py`. I know the change was long requested, and I'd like to confirm the cause with Wen Li before we decide anything.

**What would help**
1. 30 minutes with you, and Wen Li if possible, to go through this.
2. Your read on whether the missed-versus-declined scoring was deliberate.
3. Your view on how to handle the four responders in the meantime. I'm contacting their handlers.

I'm also taking the data to Helen once Wen Li and I have talked.

Thanks,
[Your name]
