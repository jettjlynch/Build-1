---
name: Bill Bellamy
state: seed
generated: 2026-09-13
booking: NONE — this brief was generated as the first full end-to-end test of the guest-research skill (M1–M6) against a seed-state dossier, not a confirmed booking.
---

# Prep Brief — Bill Bellamy

**This brief does not meet the build plan's own format, and that's the actual finding of this run, not a defect to paper over.** The spec calls for a hero fact plus five supporting facts, all VERIFIED. This dossier has **exactly one VERIFIED fact, total.** Eight more scored ≥7 from the Scorer but came back UNVERIFIED from the Verifier — per the guardrails, an UNVERIFIED tag is a wall, not a warning, so none of them can appear below as usable material, regardless of how good their numbers look. Rather than force a six-fact arc by quietly using UNVERIFIED facts (exactly the failure mode this whole pipeline exists to prevent) or padding with generic material, this brief shows what's actually usable — one fact — and documents the rest as open work, not hidden gaps.

**Guest in one line:** Comedian, actor, and TV host — MTV VJ in the early-to-mid 1990s, Def Comedy Jam-era stand-up, film roles including *Fear*, *How to Be a Player*, and *Any Given Sunday*.

**Career-payoff count (per the rubric addition): 1 of 1 usable facts carries career payoff.** Across all 18 facts this dossier ever scored, the split is 11 yes / 7 no — but with only one fact actually clearing verification, this count is nearly meaningless on its own this time; it's reported because the rubric requires it every time, not because it says anything useful about this particular guest yet. Revisit once more facts are verified.

---

## Best available fact + artifact + source (NOT a qualifying hero fact)

**Named plainly: this does not clear the ≥10 hero-fact bar.** It's the best material this dossier has, used here as the lead fact because something has to open the conversation — not represented as a hero moment.

**Fact (9/12 — Obscurity 1, Era 3, Specificity 3, Verification 2 — VERIFIED):** Rutgers University's own official news office ran a feature stating Bellamy attended Rutgers–New Brunswick, graduated 1989 with a major in economics and a minor in marketing, performed stand-up as a student at the RAC and Livingston Gym, hosted a coffeehouse event and talent/fashion shows, and called himself "the Rutgers comedian." Independently corroborated by New Jersey Monthly, which adds a specific, non-contradicting detail: he won a $200 comedy-competition prize at the Corner Tavern.

**Artifact:** No candidate in `artifacts.md` clears a confirmed Specificity=3 this run. The best option isn't an object at all — it's a live research request to Rutgers Special Collections & University Archives for a dated 1987–89 *Daily Targum* issue that might cover the coffeehouse/talent-show events named above (Specificity 2, real upside to 3 if an issue is actually found naming him). See `artifacts.md` for why the other four candidates (Corner Tavern ephemera, a 1989 yearbook, the Roanoke Times page, Rascals ephemera) don't clear the bar either.

**Source:** rutgers.edu/news, "Bill Bellamy's Career in Comedy Started on Stage at Rutgers"; New Jersey Monthly, "Bill Bellamy Reflects on Being 'The Rutgers Comedian' Before Big Break." Search-snippet only on both — no direct fetch achieved this run, no fetch-relay used (unlike the Nick Khan run's one-time manual assist). Capped at Verification=2 accordingly.

---

## Supporting facts: only one exists. The rest is open work, not a hidden gap.

The build plan asks for five. Here is what's actually there instead — eight facts that scored ≥7 numerically but came back UNVERIFIED, listed so nothing is silently missing from this record:

| Fact | Score | Status | What would unlock it |
|---|---|---|---|
| Ray Romano opener at Rascals, 1990–91 | 8/12 | UNVERIFIED — self-report only, no third-party corroboration anywhere | A reply from Mark Magnusson (venue owner) or Ray Romano himself — both outreached, see below |
| Seton Hall Prep, Class of 1983 | 9/12 | UNVERIFIED — second source's independence unconfirmed | A reply from Matt Cannizzo or the school's own alumni office |
| CA registry filing, "Bill Bellamy Entertainment, Inc." | 8/12 | UNVERIFIED, high-priority flag — same shape as the Nick Khan CA-Bar near-miss; no identity link to the comedian confirmed | An actual fetch of the primary bizfileonline.sos.ca.gov record — no person can resolve this, only direct registry access |
| Roanoke Times, Jan 1994, TelePrompTer quote | 8/12 | UNVERIFIED — real, unresolved conflict with a differently-worded LA Times attribution | A reply from N.F. Mendoza (likely original LA Times author), outreached below |
| Arsenio Hall Show 1992 + Rascal's Comedy Hour + HBO special | 6/12 (corrected down from 7) | UNVERIFIED, below usability bar — one source dressed as two | An actual fetch confirming IMDb's cast credit, or a second independent source |
| Baltimore Sun, 1994, two VJ-debut pieces | 7/12 | UNVERIFIED — same outlet twice isn't independent corroboration | A fetch of either archive page, or a second outlet |
| X account created April 2009 | 5/12 (corrected down from 7) | UNVERIFIED, below usability bar — Obscurity was over-credited | Not worth chasing further; too thin even if confirmed |
| Salon.com, 2013, Carson Daly "You're MTV's Bill Bellamy!" | 7/12 | UNVERIFIED — quote wording unconfirmed | A fetch of the Salon article |

Below the ≥7 bar entirely (kept in `dossier.md` as data points, not chased further here): the Wall Street sales job, the Russell Simmons/Uptown Comedy Club discovery story, the Rutgers pageant story, the 1995 MTV/Tupac and MTV/Michael Jackson hosting credits, the Yellow Scene Magazine piece, the Last Call w/ Carson Daly reminiscence, and the Life and Def jacket-copy mention.

---

## Referral chain

**None exists.** `referred_by` is `null` — this is a cold, standing-research seed, not an inbound referral.

---

## Contacted people and what they said

**Nobody has been contacted.** Five emails exist as drafts in `outreach/` — Mark Magnusson, N.F. Mendoza, Matt Cannizzo, Ray Romano, and Karen Bellamy (Bill's sister, handled with extra care as a family contact, not an industry one) — all pending Jett's review, none sent. Every one of them targets a specific open verification question from the table above, not a generic ask. If any reply before this guest is actually booked, the reply should be typed into `dossier.md` as a new VERIFIED fact (a real person on record, replying to a direct question) — which, notably, would very likely be this dossier's *first* fact to reach Verification=3 the honest way, since none of the current nine have.

---

## Questions, sequenced

Only enough verified material exists for an opening, not a full arc. Sequenced as far as it honestly goes:

1. **Open (grounding, not a scored fact).** "Before anything else — you've worn a lot of hats, MTV, stand-up, film, podcasting. Which one actually feels like the real you?"
2. **Rutgers (9/12, VERIFIED — the only fact available).** "You've told the story of getting your start doing stand-up at Rutgers — the RAC, Livingston Gym, calling yourself 'the Rutgers comedian.' There's even a detail about winning $200 at a comedy competition at the Corner Tavern. What actually got you on stage there the first time?"
3. **Everything after this point is not yet buildable.** The arc's middle (a hero fact) and its remaining supporting beats depend on facts that are currently UNVERIFIED. Building further questions from them now would mean asking Bellamy about material this dossier can't stand behind — exactly backwards from the point of the research. **Do not extend this arc until at least the CA registry identity question and one or two of the outreach replies come back**, at minimum.
4. **Close (mandatory referral ask, buildable regardless of fact state).** "Last thing — who should I be talking to next? Who's a name people would be surprised I even know to ask about?"

---

## Do not use

**Cut on the hard rules:** none this run — the Scorer and Verifier found zero hard-rule violations across all 18 facts.

**Excluded for other reasons — UNVERIFIED, not hard-rule cuts:** every fact in the supporting-facts table above. Restated here per the format's own requirement, even though it's the same list: nothing UNVERIFIED enters a brief, and that's most of what this dossier currently has.
