# Dispatch 4.2: contradictions, gaps and data findings

Sources: rook-wiki (About Rook, Glossary, Team directory, Releases, Q3 roadmap, Product briefs, 4.2 comments), Priya's handover, `00-rook/code/dispatch-routing`, rook-database (data 29 Jun to 6/7 Sep 2026).

## Contradictions

**Code vs. docs**
- Glossary says missed is "recorded separately" from turned down. `history.py` `record_declined` treats both the same.
- Release note says proximity "weighted up". `config.py`: proximity 0.45 -> 0.60, recent acceptance 0.40 -> 0.25 (a 37% cut to acceptance). Handlers weren't told.
- Priya says a nearby responder could "sit unoffered". README: everyone available is on the list; ranking only sets order.
- Glossary lists availability as a routing input; in code it is a filter. Glossary says Dispatch writes the Availability Record; `availability.py` says routing never changes it and responders/handlers set it in the app.
- Roadmap commits "Ping timeout tuning" for 4.2; what shipped was a flat cut from 90s to 60s.

**Wiki vs. handover**
- Who pulls numbers: Priya says the engineering manager; Marcus says only "rough"; Team directory says Ravi Menon owns weekly reporting.
- Seasonality: Priya and Marcus say August is always soft; no data supports it, and Priya's own handover admits fewer pings are being taken.
- "Monthly release train" vs. actual gaps of 10 weeks (4.0 to 4.1) and 8 weeks (4.1 to 4.2).
- Bulk Callout (shipped 4.1) and Routing Override Audit Log (shipped 4.0) briefs still read as proposals.
- Glossary "Mutual aid" and roadmap "Shared cover between responders" are probably the same item.
- Roadmap owners all still Priya (last reviewed 30 Jun); Handler Phone App brief (8 Sep) names the product designer; directory still shows Wen Li away in August.
- Halloran: handler of Sgt. Bulwark in the database, but treated as the Supply quartermaster contact in the briefs; not in the directory.

## Missing
- No prior-year data, per-responder scores, or written description of how pinging is decided.
- `feedback/` folder is empty (no interviews, no ticket breakdown).
- Availability Confidence: committed for 4.2 but absent from release notes, changelog, glossary and code.
- No Supply people in the Team directory; no owner for the 4.3 Supply commitment.
- Security Policy 4.1 text.

## Data findings (database)

| Week of | Pings | Accept % | Missed % | Tickets |
|---|---|---|---|---|
| 29 Jun | 172 | 76.7 | 1.7 | 6 |
| 6 Jul | 170 | 77.1 | 2.4 | 6 |
| 13 Jul | 174 | 76.4 | 3.4 | 8 |
| 20 Jul | 176 | 75.0 | 2.8 | 5 |
| 27 Jul | 170 | 77.6 | 2.4 | 7 |
| 3 Aug | 172 | 77.9 | 1.2 | 7 |
| 10 Aug (release 12 Aug) | 177 | 54.2 | 21.5 | 20 |
| 17 Aug | 158 | 65.8 | 17.7 | 27 |
| 24 Aug | 162 | 66.7 | 14.8 | 32 |
| 31 Aug | 165 | 72.7 | 12.7 | 25 |

- Unfilled callouts: 5.5% before 12 Aug, 11% after. Callout volume dipped about 10%.
- Four responders (Vesper/Aunt Dot, Farlight, The Undertow, Meteor Mite/Kip) fell from about 49 pings a week combined to 3 by 31 Aug; none has taken a ping since 19 Aug. Everyone else's pings rose from about 123 to 162 a week.
- Aunt Dot and Kip filed no tickets; Farlight's and The Undertow's handlers filed many ("Is Farlight still on the list?").

## Likely mechanism (inference; ranking scores are not in the database)
A decline or a miss costs 0.12 and an accept earns 0.08. A score only moves when someone is pinged and never decays (2019 TODO in `history.py`). 4.2 cut the acceptance weight and the ping wait to 60s, so misses push responders down the list and they rarely get a chance to recover. Missed pings are scored like declines.

## Next steps
Confirm with Wen Li, then take it to Helen; ask Ravi for per-responder numbers; speak to Aunt Dot and Kip.
