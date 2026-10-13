# Draft: message to Marcus Oyelaran, with the option brief (not sent)

Attach: `drafts/option-brief-for-helen.md` (copy it into an email or doc first). Note the brief is addressed to Helen; either tell Marcus that, or strip the "What I need from you" section before sending.

**Subject: 4.2 routing: options, and what I need from engineering**

Hi Marcus,

I've written up the 4.2 options in a short brief, attached. I'd like your engineering view on it before I take anything further. I haven't taken it to Helen yet, and the cause is still unconfirmed: I've asked Wen Li, and I'll tell you what she says.

The short version:
- Acceptance held at about 77% until 12 August, then dropped to 54% and has partly recovered. Missed pings went from 1-3% to 21%, and they're still at 13%.
- Four responders went from about 49 pings a week combined to 3. The rest absorbed the load.
- My working theory is that the 60s ping wait causes misses, and misses lower a responder's score with no way to recover. A replay of one responder's pings fits this. I can't see the real scores.

The options in the brief are restoring the 90s wait, counting a miss as less than a decline, letting scores recover over time, and a one-off reset for the four. `config.py` says to tell you before changing anything in it, so I'm telling you now. I'm not proposing a change yet.

What I'd like from you:
1. How big is each option, and which release could each land in? The 90s wait looks like one config value, but I'd rather hear it from you.
2. Do scores in `history.py` persist anywhere, or do they reset when the service restarts? That decides whether a one-off reset for the four is even possible.
3. Was there a reason for the 60s wait, and does anything depend on it?
4. 30 minutes this week, with Wen Li if possible.

Thanks,
[Your name]
