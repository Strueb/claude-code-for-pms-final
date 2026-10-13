# Draft: message to Helen Achebe, with the option brief (not sent)

Attach: `drafts/option-brief-for-helen.md` (copy it into an email or doc first). Send after Wen Li replies, or tell Helen the cause is still unconfirmed, as below.

**Subject: 4.2 routing: options and what I need from you**

Hi Helen,

I've put together a one-page brief on the 4.2 problems and the options for fixing them. It's attached. I'd like your view before I take anything to engineering.

The short version:
- Acceptance held at about 77% until 12 August, then dropped to 54% and has partly recovered. Missed pings went from 1-3% to 21%, and they're still at 13%.
- Four responders went from about 49 pings a week combined to 3. The "recovery" partly reflects them dropping out. The data doesn't support seasonality, though we have no prior-year numbers to rule it out.
- The likely cause is in the ranking: the shorter 60s ping wait causes misses, and misses lower a responder's score with no way to recover. This is my inference from the data and code, not confirmed. I've asked Wen Li, who built the ranking, and I'll update you when she replies.

The options in the brief are restoring the 90s wait, counting a miss as less than a decline, letting scores recover over time, and a one-off reset for the four. My provisional recommendation is the 90s wait first, then a scoring change once Wen Li confirms how it works. I'm not recommending we revert the acceptance weight, because my replay suggests that would make it worse.

What I need from you:
1. Your view on those options, and whether the 60s change has a commitment behind it that I should know about.
2. Who else needs to approve a routing change.
3. 30 minutes to go through the Q3 commitments. The roadmap hasn't been reviewed since 30 June, and Availability Confidence was committed for 4.2 but isn't in the release.

I'd rather you hear this from me now than find it in the weekly numbers. I'm happy to walk through it whenever suits.

Thanks,
[Your name]
