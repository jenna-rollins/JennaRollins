# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (started late Aug 2026; predecessor Priya Raghunathan left 21 Aug with no overlap). Wiki roadmap data is stale (last reviewed 30 Jun); verify before relying on it.

### Company and products
Rook Industries (founded 2014, HQ Site Aleph) sells coordination software to independent masked responders and the handlers/quartermasters who support them. Subscription, priced per active responder. Monthly release train; 4.x point releases.
- **Rook Dispatch** (mine): availability, proximity, who gets pinged for a callout, and whether they take it. Users: handlers (console, desktop) and responders (phone).
- **Rook Supply**: gear requisitions, maintenance, failure reports. Users: handlers, quartermasters.
- **Confidentiality:** Rook never stores responder legal identities and can't map cover identities to them (Security Policy 4.1). Never try to work out who anyone is; don't design features that assume a mapping.

### People (comments on the 4.2 page use roles only)
| Role | Person | Notes |
|---|---|---|
| Director of Product (my boss) | Helen Achebe | Owns roadmap and commitments; gives room. |
| Engineering Manager | Marcus Oyelaran | Runs Dispatch eng; candid; first stop for doubts; can pull rough numbers. |
| Staff Engineer | Wen Li (Berlin) | Built the ping-ranking logic; no written doc exists, so talk to her. |
| Product Designer | Sofia Marino | Console and phone app; ran the September interviews. |
| Support Lead | Nadia Hoffmann (Berlin) | Owns tickets; hears handler complaints first. |
| Data Analyst | Ravi Menon (Singapore) | Weekly report on how often responders take pings. |
| Former PM | Priya Raghunathan | Left 21 Aug; Marcus has her number. |

### Vocabulary
- **Callout**: request for a responder to attend an incident. **Ping**: one callout offered to one responder. Outcomes: **taken**, **turned down**, or **missed** (ping wait ran out). Turned down and missed both move it on but are recorded separately.
- **Ping wait**: how long a ping stays on a phone before it counts as missed (same for everyone).
- **Acceptance rate**: pings taken / all pings. Headline metric; **time-to-accept** is the median seconds to take it.
- **Routing priority**: score ranking responders. Inputs: proximity (travel-time), availability, capability match, recent acceptance history. Turning down or missing pings lowers that history, so lowers later rank.
- **Coverage gap**: no available responder had the needed capability tags. Not the same as low acceptance.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation.
- **Responder Availability Record**: written by Dispatch, read by Supply. **Mutual aid** (cross-area cover): unsupported; Q4 exploration.

### Release history (Dispatch)
- 4.0 (7 Apr): new console nav, responder profile redesign, routing-override audit log.
- 4.1 (16 Jun): travel-time proximity, bulk callout, push reliability.
- 4.2 (12 Aug): **proximity weighted up vs. recent acceptance**; **ping wait cut 90s to 60s**; console filters persist; 3 fixes.

### Where things stand
**The 4.2 problem (my top issue).** Since 4.2, fewer pings are taken and handler complaints are up. Callout tickets ~3x normal since 13 Aug and flat since (as of 26 Aug): about two-thirds "phone never goes off", one-third "gone before I could answer". The one-third is explained by the 60s ping wait. The two-thirds is not explained.
- Priya's read (in handoff and comments): mostly seasonal (August is always soft), will recover in September. This was never tested with data. Treat as a hypothesis, not a finding.
- At least three things moved together: ping-ranking change, ping wait cut, and season. Don't assume one cause; separate them in the data (rook-database; Ravi's weekly numbers).
- Open question from Marcus (14 Aug), unanswered: does the new weighting apply to responders who've been turning jobs down, or only others? The config doesn't distinguish. Possible feedback loop with the recent-acceptance component. Wen Li owes an answer.
- Priya advised against reverting 4.2: the change was a long-standing ask from wide-area responders. Marcus wants me to form my own view before regrouping on the 4.2 picture. Don't present a conclusion before the evidence.

**Q3 roadmap vs. reality** (last reviewed 30 Jun, all Priya-owned):
- Ping-ranking change (4.2): shipped. Ping timeout tuning (4.2): shipped as the 90s to 60s cut.
- **Availability Confidence (4.2, Committed): not in the 4.2 release notes; slipped.** Priya says "a couple" of items were squeezed out and the conversation with Helen about which are still Q3 commitments hasn't happened. I need to have it.
- Requisition approval chains (4.3, Committed): Supply item. Brief starts with one ask (second approval over $2,000) then piles on four more features. Needs scoping.
- Handler phone app and shared cover between responders: Q4, Exploring.

**Product briefs** (wiki "Product briefs"): Bulk Callout, Handler Phone App (8 Sep, Sofia, unstaffed, one handler quote as evidence), Requisition Approval Chains, Routing Override Audit Log. Bulk callout and the audit log appear in 4.0/4.1 release notes but their briefs still read as unbuilt, so the briefs are out of date.

**Other debts:** Nobody has written down how ping ranking works; Priya asked me to. She admits moving faster than she checked, so the least-examined parts of the product are the riskiest.

### First moves
1. Pull acceptance rate by week (before/after 12 Aug, vs. prior years) and split taken / turned down / missed.
2. Get Wen Li's answer on how the new weights treat responders with low recent acceptance.
3. Talk to Helen about which slipped items are still Q3 commitments.
4. Set up a standing 15 minutes with Nadia; get her ticket breakdown.

### Learned in session 1
- rook-wiki tools: `read_page` takes `page`, `read_database` takes `database`; both take the id from `search_wiki`.
- The wiki's Supabase function URL returns 405 to a plain fetch; use the rook-wiki tools, not the URL.
- The rook-database has a `handlers` table (15 rows); the acceptance data for the 4.2 question hasn't been queried yet.
- Wen Li's answer on how the new weights treat low-acceptance responders is still open, as is the Availability Confidence decision with Helen.

- Routing code is in `00-rook/code/dispatch-routing/`. In it, a missed ping is penalized the same as a turned-down one (`history.record_declined`, -0.12 vs +0.08 per accept), and the score never decays (2019 TODO). Combined with the 60s ping wait, low-scored responders may rarely get pinged at all. This is a hypothesis to test in the data, not a finding. Marcus's 14 Aug question is partly answered: the weights apply to everyone.
- The pings, callouts and tickets tables only run 29 Jun to 6/7 Sep 2026, so there is no prior-year baseline for "August is always soft" and no data on whether September recovered. Correction to First moves above: "vs. prior years" isn't possible with this data.
- The four Sept interviews (wiki, Research) describe pings vanishing faster and some responders going quiet while others get everything. They were run for the console redesign, not 4.2. Halloran, in the Requisition brief, wants a faster approval queue, not a second approval step.
- Open: Ravi Menon owns the real weekly acceptance numbers and nobody has asked him. Availability Confidence has no brief, definition or code. Rook Supply has no PM in the Team directory.
- 4.2 contents confirmed from the release notes, the 4.2 page and the routing changelog: weights went from proximity 0.45 / recent acceptance 0.40 to 0.60 / 0.25 (capability stays 0.15), ping wait 90s to 60s, filter persistence, three fixes. The accept credit, decline penalty and lack of decay weren't touched in 4.2 (unknown for earlier releases).
- Inferences, not facts: that roadmap "Ping timeout tuning" shipped as the 60s cut, and whether Availability Confidence was dropped on purpose or slipped. Only Helen can say.
- Nobody is assigned to reconcile the Bulk Callout brief (no owner) or the Routing Override Audit Log brief (eng manager's team). I haven't checked that what shipped matches what they proposed.
- More mismatches: only three releases in about six months although the train is "monthly", none listed after 12 Aug; the glossary (4 Aug) predates 4.2 and says missed is recorded separately while the code treats it like turned down; ping wait is also called "ping timeout" and "offer timeout"; the Handler Phone App brief asks for more than the one Aunt Dot quote supports.
- Not yet looked at: the support_tickets contents, pings by week, and the Rook Supply wiki page.

### Learned in session 2
- Pings go one responder at a time in ranked order (no overlap, no multi-take, nobody races). The 60s wait is confirmed in `config.py:9` and `offer.py:42`, and in the data (missed pings move on after 61-63s vs 91-93s before 12 Aug). Since 12 Aug, first pings missed rose from 2.7% to 21.4% while declines barely moved; callouts with no take rose from 5.5% to 11.1%.
- Four responders (Vesper, The Undertow, Meteor Mite, Farlight) went from about 12 pings a week to 1-2 over roughly three weeks, with misses over 50%. Their misses spiked in the week of 10 Aug while volume was still normal, which the score-erosion idea doesn't explain. Before 4.2 their decline times (about 21-28s) were nothing unusual, and their handlers say availability is set correctly (a handler claim, not a system record).
- The database has no take time, push delivery time, device or app version, or availability data. The wiki has no SLA and no responder protocol for answering pings; I chose Option B (a ping means "can you take this", accept first, get ready after) but haven't cleared it with Helen. Dot's rule of keeping the phone in a pocket is the only mention of phone habits, and nothing mentions watches.
- Tickets 3048, 3077, 3078, 3052, 3113 and 3123 each match a missed ping by timestamp (my inference). Stormwrack and Nightwell were pinged within about 90 seconds of taking another job.
- Open: push delivery logs, accept-tap times and app versions from engineering (draft to Marcus, not sent); whether `push_to_device` changed in 4.2 (the notes only list a duplicate-push fix); Wen Li on score changes; Helen on whether 60s should stay.
- support_tickets has 147 rows (40 filed 29 Jun-10 Aug, 107 from 12 Aug). 45 are new since 4.2 and all still open: 15 "gone before I could answer" and 30 "phone quiet", the same one-third/two-thirds split Nadia described. The quiet tickets are about Farlight (10), The Undertow (10), Corporal Ashgrove (5) and Halfmoon (5); Ashgrove's and Halfmoon's daily pings also fell about 20%, so the quiet problem is not only the four responders I flagged earlier. Vesper and Meteor Mite have no tickets at all.
- Ticket volume outside those two groups is also up (about 2.3 a day vs 0.9), but 27 of the 107 reuse an earlier subject line and 15 are word-for-word repeats under another responder, so don't read it as 62 separate problems. Duplicate-notification tickets stopped after 4.2 (3 before, 0 after); filters now generate about 12 problem tickets since persistence shipped.
- The four interviewed handlers (Dot, Ambrose, Halloran, Kip) filed one ticket between them (3043, Ambrose); the 11 handlers who file the rest were never interviewed. Ambrose's interview contradicts his ticket on whether he watched the clock and on when it happened. Interviews stress "gone" and text size; tickets are mostly quiet phones plus a long tail of paired one-off requests.
- Before 4.2, responders turned pings down in about 17-28s on average and never took longer than 40s to decline (a proxy; there is no take time). Next step I'd suggest: interview Farlight's and The Undertow's handlers, and ask Nadia whether paired tickets are separate incidents.
