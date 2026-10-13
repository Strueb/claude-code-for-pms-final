# Draft: message to Wen Li, ranking walkthrough (not sent)

Separate from message-to-wen-li.md (the 4.2 findings). Send after Wen Li replies to that one, or combine the two asks into one meeting.

**Subject: Could you walk me through how ranking works?**

Hi Wen Li,

Priya's handover says nobody has written down how Dispatch decides who gets pinged, and that talking to you is the best way to understand it. She asked me to write it, and I'd like your help to get it right.

I've read the `dispatch-routing` code, so I have the outline. Ranking combines proximity (as a travel-time estimate), recent acceptance and capability match, with weights in `config.py`. A responder's recent-acceptance score moves by +0.08 for an accept and -0.12 for a decline or a miss. Offers go down the ranked list one at a time with a 60-second wait.

What I'd like to learn from you is what the code can't tell me:
1. **Why the design is the way it is.** Why does a miss count the same as a decline? Why was there no decay on the acceptance score (the 2019 TODO in `history.py`)?
2. **What the weights are meant to do.** How did you choose them, and what did you expect to happen when 4.2 shifted weight toward proximity?
3. **Edge cases.** What happens to a new responder, a responder who comes back from time away, or a callout where nobody is available?
4. **What isn't in the code.** Does the Availability Record, the capability tags or anything on the phone side change the order?
5. **What you'd change.** If you could change one thing about the ranking, what would it be?

I'd write it up in plain language for the PM, support and handler teams, and send it back to you to correct before anyone else sees it. 45 minutes would be plenty, and I can work around Berlin time.

Thanks,
[Your name]
