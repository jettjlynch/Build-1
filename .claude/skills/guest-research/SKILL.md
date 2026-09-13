---
name: guest-research
description: Automates podcast guest research to the standard of interviewer Nardwuar — builds a standing, ever-growing dossier per guest, scored against the Nardwuar Bar, starting years before a booking exists rather than the week of. Use to start research on a new potential guest (`seed <name>`), to grow an existing dossier via the Sweeper/Scorer/Verifier pipeline (`sweep <name>`), or, once a booking is confirmed, to compile everything into a prep brief (`brief <name>` — not yet implemented, see Roadmap).
---

# guest-research

Companion skill to `nardwuar-research-methods.md` and `guest-research-engine-build-plan.md`. Read those first if you haven't — this skill is the automated half of what those documents describe. The short version: Nardwuar's edge was never a secret source. It was (a) standing files that accrue for years before a booking, (b) volume of legwork most people skip, (c) people who knew the guest before they were known, (d) one artifact that proves the work, and (e) careful sequencing. This skill exists to do (a) and (b) without quietly collapsing into "the first page of Google in nicer prose" — which is why the rubric below is the load-bearing part of the whole system, not a formality.

## The Nardwuar Bar — the quality rubric every fact must pass

This is the core asset. Without it the system is a summariser. Every candidate fact gets scored 0–3 on each axis; the brief only uses facts scoring **≥7/12**, and the **"hero fact" must score ≥10**.

**Obscurity (0–3).** 0 = on their Wikipedia/first Google page. 1 = in a major profile. 2 = only in a niche/local/old source. 3 = not indexed anywhere obvious — found via archive, print, or a person.

**Era (0–3).** 0 = current news. 1 = post-fame. 2 = the transition year. 3 = pre-fame — school, first job, first failure, the thing they wanted at 12 and couldn't have.

**Specificity (0–3).** 0 = category ("he was into skating"). 1 = named thing. 2 = named thing + date/place. 3 = a single moment with emotional charge (the URB magazine, two dollars short).

**Verification (0–3).** 0 = single unverified source. 1 = single reputable source. 2 = two independent sources. 3 = primary artifact exists (the actual clipping, the actual record, the person on record).

Score each axis only against its own definition — verification strength never inflates Obscurity, and a fact's obscurity never inflates Verification, however good the fact looks once totaled. (Logged 2026-09-13: a fact was briefly mis-scored in both directions this way — once by the Scorer overcrediting Obscurity via search-snippet corroboration, once by a collaborating session suggesting the same error in reverse after a primary-source read. Both were caught before being written in.)

**Career payoff (yes/no — tracked, not scored).** Every fact also carries this tag: **yes** if the fact's significance is that it explains or foreshadows the guest's professional success; **no** if it stands on its own with no professional logic attached — pure identity, family, place, or personality, nothing that cashes out into "and that's how they made it." This isn't part of the 0–12 total and never affects whether a fact clears the usability or hero-fact bar. It exists because a dossier can score well on every axis, fact after fact, while still only ever finding "the origin story of how this person got successful" — which is a real, subtly different thing from finding out who they are (logged 2026-09-13, after a cold read of Nick Khan's first `prep-brief.md` found every single fact in the arc resolved to career logic). `brief` mode must report the count of yes/no across the facts it actually uses, explicitly, at the top of `prep-brief.md` — e.g. "5 of 6 facts carry career payoff; 1 does not." **If the count of "no" is zero, say that plainly as a named gap — never omit the line because the number is embarrassing.** A brief that goes quiet on this exactly when it would look bad is the failure mode this tag exists to catch.

**Verification cap: no fact may score 3 unless its cited source has actually been directly fetched and read by an agent** — the full page, or an archived snapshot, not a search engine's paraphrase of it. If that hasn't happened, Verification is capped at 2, no matter how many independent outlets appear to agree in search snippets — corroboration across snippets is not the same as anyone having actually seen the source. This cap exists because this environment's egress policy blocks WebFetch and curl to essentially every research domain (confirmed by direct testing: identical `connect_rejected` / 403-at-CONNECT failures from both the WebFetch tool and a raw curl call, logged by the proxy itself as an organization policy denial — see the M2 sweep section of `Guests/nick-khan/dossier.md` for the full diagnostic). It is a structural fact about this deployment, not a permanent design choice.

**Resolved (2026-09-13): the fetch it requires does not have to happen in the literal current conversation.** A separate session with unrestricted WebFetch can fetch a source and relay the confirmed full text — this counts as satisfying the cap, *provided* the receiving session doesn't just take the relay's word for it. Before accepting a relayed fetch as Verification=3, independently re-check what you can (a fresh search for the exact quote/detail, a byline/date sanity check) and write down plainly what was and wasn't independently corroborated versus taken on trust — see `Guests/nick-khan/dossier.md`'s "Fetch-relay upgrade" section for a worked example, including a case where only part of a relayed fact could be independently confirmed. Never upgrade a fact to Verification=3 on a relay claim alone, with zero independent check, no matter how specific it sounds — that's exactly the failure mode that produced the CA-Bar confabulation earlier in this same dossier. **What's still manual, and what to automate next:** right now the hand-off is a person copy-pasting text between two chat sessions. That's the target for automation — not the fetching itself, which this proved already works — ideally via this environment's own cross-session messaging so a Sweeper or Verifier agent can request a fetch from a fetch-capable sibling session directly.

Two hard rules layered on top: **fact source must be public or consensually given** (the Lil Uzi Vert "he know too much" reaction is the failure mode — surveillance-adjacent facts get cut even if they score well), and **no fact from a private individual's personal life who isn't the guest** (ex-partners, children, medical anything).

These two hard rules override any score. A fact that scores 12/12 but fails a hard rule is cut, full stop — it never enters the dossier as usable, regardless of how good it is.

## Dossier folder schema

Per the build plan (§2), the canonical target is a per-guest standing file in the Obsidian vault:

```
/Podcast/Guests/<guest-slug>/          ← the standing file (Obsidian vault)
  dossier.md                           ← living, agent-appended, scored facts
  sources.md                           ← every URL/person/artifact, with fetch date
  people.md                            ← pre-fame contacts identified + outreach status
  artifacts.md                         ← gift candidates, where to buy, price, status
  outreach/                            ← drafted emails, one file each, never auto-sent
  prep-brief.md                        ← generated only when a booking is confirmed
```

**Implementation note (M1):** this session has no live connection to the Obsidian vault (JJ Second Brain) — the vault MCP isn't available here. Until it is, `seed` creates the same structure at `Guests/<guest-slug>/` in this repo instead of `/Podcast/Guests/<guest-slug>/` in the vault. The schema, filenames, and frontmatter are otherwise identical, so migrating a dossier into the vault later is a straight copy. Do not silently start writing to the vault path once MCP access exists — confirm with Jett first, since it may mean moving already-seeded dossiers rather than duplicating them.

Every dossier's `dossier.md` opens with frontmatter:

```yaml
---
name: <Guest Name>
state: seed
created: <YYYY-MM-DD>
referred_by: <who referred this guest, or null>
---
```

`state` progresses `seed → ... → booked` as the pipeline matures (M2+); M1 only ever writes `seed`.

## Guardrails (non-negotiable)

- Drafts never send. No email, DM, or form submission is automated.
- Outreach identifies the sender truthfully. No pretexting, no posing as press if not press.
- Public or consensual sources only. No scraping behind logins, no data brokers, no people-search sites.
- The two hard rules from the rubric override any score.
- Unverified facts are quarantined from the brief. An UNVERIFIED tag is a wall, not a warning.
- Every fact carries its source and fetch date, so anything can be traced back on air if challenged.
- **`brief` mode must produce a `relay-request.md` whenever UNVERIFIED facts remain at the end of the run** (added 2026-09-13). See below for format and rationale — this is a required structured output of `brief`, not something produced only when it happens to be top of mind.

These apply to every mode below, including ones not yet built.

### `relay-request.md` — a human fetch-relay is a pipeline step, not a fallback

Every guest dossier this skill has actually run against (Nick Khan, Bill Bellamy) has converged on the same finding: this environment's egress policy blocks WebFetch/curl to essentially every research domain (see the Verification cap above), so the Sweeper/Scorer/Verifier pipeline alone reliably produces a handful of UNVERIFIED ≥7 facts and very little at Verification=3 — and every genuine hero-tier fact either dossier has produced so far came from a human manually fetching one page and relaying it back in. On Bill Bellamy specifically, a single relayed fetch turned out to carry enough content to upgrade three separate facts at once. That is not a workaround to feel slightly bad about — it is the highest-leverage action available in this pipeline, and treating it as a demoted fallback ("if all else fails, ask Jett to fetch something") would mean quietly not asking for the one thing most likely to move a dossier from thin to real. **`relay-request.md` makes that ask a standing, structured, repeatable output of `brief` instead of something dependent on whether an agent happens to think of it that session.**

**When:** generated automatically at the end of every `brief` run in which one or more facts remain UNVERIFIED at ≥7 (which, given the environment constraint above, will be nearly every run until that constraint changes).

**Format — `Guests/<guest-slug>/relay-request.md`:**
- **2 to 4 items, never more.** This is a short, decisive list of what to spend a real human fetch on this round, not a dump of every open question in the dossier — `sources.md` already holds the exhaustive record.
- **Ranked by leverage, not by score alone.** Leverage means: how much would one successful primary read move this fact, weighed against how likely that read is to actually succeed. A 9/12 UNVERIFIED fact one Wikipedia fetch away from a plausible 10/12 outranks an 8/12 fact whose only remaining source is a paywalled archive or an in-person-only special-collections visit — the second may be worth doing eventually, but it's not this round's best use of a fetch. A fact that's already structurally unreachable (per `sources.md`'s "consulted, confirmed empty or structurally blocked" section) does not belong on this list at all, however high it scores — there's nothing to relay-request there.
- **Each item states:** the fact it bears on (by number and one-line description), the **exact source URL** to fetch (not a domain, not "search for X" — if no single URL exists to fetch, as with a pure self-report anecdote with zero associated source, that fact doesn't belong on this list), and **one line on what a direct fetch would need to confirm or resolve** — specific enough that whoever does the fetch knows exactly what they're checking for, not just "read this page."
- Ends with a one-line honest note on anything scored ≥7 and UNVERIFIED that was deliberately left off the list, and why (already attempted and failed, no fetchable URL exists, structurally blocked) — so the absence of a fact from this list reads as a decision, not an oversight.

**What this is not:** it is not a to-do list for this skill to act on unattended, and it never claims a fact is upgraded until the same independent-spot-check discipline in the Verification-cap section above has actually been applied to whatever comes back. It is an ask, sized and prioritized, for Jett to decide whether to spend a fetch this round — see `Guests/bill-bellamy/relay-request.md` for a worked example.

## Entry modes

### `seed <name>` — implemented

Creates the standing dossier for a new guest before any booking exists. This is the only mode M1 implements.

1. Slugify `<name>` (lowercase, hyphens) to get `<guest-slug>`.
2. Create `Guests/<guest-slug>/` (see storage note above) containing:
   - `dossier.md` — frontmatter block, then a `## Candidate Facts` section, facts in descending score order. Each fact records: the fact itself, its four axis scores and total, source(s) with URL/name, fetch date, and a hard-rule pass/fail note. Facts that fail a hard rule are omitted entirely, not just scored low.
   - `sources.md` — every URL/person/artifact consulted, with fetch date, one per line, regardless of whether it produced a usable fact.
   - `people.md` — empty at seed time (M3 territory); leave a header and a note that this is populated by the People-finder phase.
   - `artifacts.md` — empty at seed time (M3 territory); leave a header and a note that this is populated by the Artifact-hunter phase.
   - `outreach/` — empty directory (M3 territory); nothing is drafted at seed time.
3. Hand-research a first pass of candidate facts (no agents at M1 — see build plan §1 milestone note: this step exists to test whether the rubric and source list actually work before automating either). Score every candidate against the rubric above, including ones that don't clear the bar — a rejected fact with its score and reasoning is useful data for later sweeper design, and the dossier's job is to hold everything found, not just what's usable.
4. Do not fabricate a source or a fetch date. If a fact can only be traced to a low-quality aggregator or an unconfirmed claim, say so in the dossier rather than dressing it up as a clean source — that honesty is what the Verification axis and the UNVERIFIED tag exist for.

### `refer <name> --from <source-guest-slug>` — implemented (M6)

The referral loop from the build plan's "who's next?" close (§3, §5). Not tied to a real recorded episode yet — nothing is booked for any guest in this repo — so this exists as a standalone command any future step can call: a real post-episode log once bookings happen, or a manual entry from Jett in the meantime. Never invoked automatically by anything in this skill today.

1. Confirm `Guests/<source-guest-slug>/dossier.md` actually exists — refuse rather than guess if it doesn't. Read its frontmatter.
2. Run the exact same folder-creation steps as `seed <name>` above (steps 1–2: slugify, create `Guests/<new-guest-slug>/` with all five files/dirs) — `refer` is `seed` with one different frontmatter value, not a separate implementation to keep in sync.
3. **The one difference:** the new dossier's frontmatter sets `referred_by: <source-guest-slug>` instead of `null`. This field has existed in the schema since M1 but had never once been set to a real value until this milestone — confirm it's an actual YAML value in the new file, not just a comment saying a referral happened.
4. Append to the *source* guest's own `dossier.md`, under a `## Referrals` heading (create it if this is the source guest's first), one line per referral: who they referred, the new guest's slug, and the date. This makes the chain traceable from either end — forward from the new dossier's `referred_by`, and backward from the source dossier's `## Referrals` list — not just forward. Never overwrite a prior `## Referrals` entry; each call appends one line.
5. Do not hand-research facts for the new dossier as part of this command — that's `seed` step 3's job (or `sweep`'s pipeline), run separately, whenever someone actually gets to it. `refer` only creates the shell and records the link; it doesn't do the research.

### `sweep <name>` — implemented (M2)

Grows an existing dossier using the actual Sweeper → Scorer → Verifier pipeline from the build plan (§2), instead of hand research. This is what "standing files that never stop growing" turns into once it's automated — later (M5) it's the same pipeline a scheduled monitor runs weekly, unattended, across every seed-state dossier. Requires a dossier already created by `seed`.

Full prompt text for every subagent below lives in `references/` — `sweeper-prompts.md`, `scorer-prompt.md`, `verifier-prompt.md`. Don't inline a paraphrase of them elsewhere in the skill; edit those files and every mode that uses them stays in sync.

1. **Sweep.** Dispatch all eight Sweeper agents from `references/sweeper-prompts.md` in parallel, one per source type (a–h). Each is scoped to exactly one source type and forced to justify why what it found isn't just a generic search's first page — if a Sweeper's own notes admit it fell back to a generic search, that's reported honestly, not hidden. Each returns raw, unscored facts with source and fetch date. A Sweeper reporting "nothing found for this source type" is a valid, honest result — never pad its output to look more productive than it was.
2. **Score.** Pool every Sweeper's raw output and run it once through the Scorer agent (`references/scorer-prompt.md`). The Scorer dedupes overlapping facts (merging sources when two Sweepers independently found the same thing — that's a real Verification signal, not noise), scores every surviving fact on all four axes with reasoning shown, applies the two hard rules (rejecting outright, not just scoring low, anything that fails one), and sorts by total. Nothing is called "verified" at this stage — the Scorer marks ≥7 and ≥10 as *pending* verification.
3. **Verify.** Every fact the Scorer marked ≥7 goes to the Verifier agent (`references/verifier-prompt.md`), one adversarial pass whose job is to try to break each fact: find independent corroboration or fail to, catch a contradiction, catch an aggregator source posing as multiple independent ones, re-check both hard rules independently. Each fact comes out VERIFIED, UNVERIFIED, or REJECTED. Only VERIFIED facts are usable in a future brief; UNVERIFIED facts stay in the dossier as a wall, not a warning, per the guardrails.
4. **Write back.** Append the pass to `dossier.md` under a new dated `## Sweep pass — <date>` heading (never overwrite or renumber prior entries — the dossier is a standing, cumulative file), sorted by score, each fact showing its full per-axis breakdown, its Sweeper source-type origin, and its verification verdict. Update `sources.md` with everything consulted, including Sweepers that came up empty.
5. Report honestly on the run: the highest-scoring fact with its full axis breakdown (not just the total), whether Obscurity actually hit 2 or 3 anywhere, and — if it didn't — which Sweeper(s) underperformed and why, the same way the M1 hand-research pass was assessed. A sweep that quietly matches M1's Obscurity ceiling is a failed test of the pipeline, not a neutral result; say so if it happens.
6. **People-finder (M3, implemented).** Run `references/people-finder-prompt.md` against the current dossier. Identifies 5–10 named, reachable pre-fame contacts, each tied to a specific dossier fact with a specific ask — never an invented name for a category. Appends to `people.md`; never duplicates an entry already there. **Mandatory non-industry check (added 2026-09-13):** every run must explicitly attempt to identify at least one contact who is *not* already a wrestling/law/media-industry figure — a non-industry classmate, a family member beyond whichever sibling already made the list, anyone from before the guest had a career worth writing about. This is a required attempt, not a required success: if none can be found, log "attempted, none found" in `people.md` plainly rather than silently dropping the requirement or padding the list with an industry contact to look complete.
7. **Outreach drafter (M3, implemented).** Run `references/outreach-drafter-prompt.md` against the People-finder's output. One drafted email per person into `outreach/`, following the build plan §3 template, sender identity honest (Jett's own name by default, per build plan §6). **Never claims a booking or date that doesn't exist** — if the dossier is still in `seed` state, the email says so rather than reusing the template's "I'm interviewing them on [date]" language as fact. Drafts only; nothing here is ever sent by the skill.
8. **Artifact hunter (M3, implemented).** Run `references/artifact-hunter-prompt.md` against the dossier. Proposes 3–5 gift candidates ranked by Specificity, each tied to a named dossier fact — a candidate that would suit any successful person in the guest's field, not this guest specifically, doesn't belong on the list, and the hunter should say so plainly about its own weaker candidates rather than oversell them. Writes to `artifacts.md`. Nothing is purchased or committed to.

### `brief <name>` — implemented (M4)

Runs once a booking is confirmed — or, as a test/build exercise, on demand against a seeded dossier that has no booking yet, provided the brief says so honestly rather than pretending one exists (see the outreach-honesty rule below). Reuses `sweep`'s full pipeline (steps 1–8: Sweeper/Scorer/Verifier, then People-finder/Outreach drafter/Artifact hunter) and adds one final phase:

9. **Question architect (M4, implemented).** Run `references/question-architect-prompt.md` against the current dossier, people.md, artifacts.md, and outreach/ state. Builds the actual interview arc and assembles `prep-brief.md`. Sequencing rules (non-negotiable): open with "who are you?"-style grounding; first scored fact used must be mid-tier (7–8); the hero fact (≥10, and only if VERIFIED — never a fact currently flagged UNVERIFIED, REJECTED, or do-not-use, however high its score) lands in the middle of the arc, never the open; a referral callback only if `referred_by` is actually set in the frontmatter, never invented; close with the mandatory "who's next?" ask, flagged for manual seeding until M6 automates it. **Outreach honesty is load-bearing here:** if outreach has been drafted but not sent, or sent but not replied to, the brief must say that plainly — never write a "contacted people and what they said" section as if a reply exists when none has come back. A brief that quietly treats an unsent draft as a data point has failed at the one thing this whole skill exists to prevent.

`prep-brief.md` format (build plan §3): guest in one line; the hero fact + its artifact + its source; five supporting facts in arc order with scores; the referral chain (or an honest statement that none exists yet); contacted people and what they said (or the honest current outreach state); the questions, sequenced; a "do not use" list of high-scoring facts cut specifically on the hard rules — kept distinct from facts excluded for other reasons (UNVERIFIED, a sourcing correction, an unresolved identity question), which get their own clearly-labeled note rather than being folded into the hard-rule list.

10. **Relay request (added 2026-09-13).** If any fact scored ≥7 by the Scorer remains UNVERIFIED once step 9 finishes, generate `Guests/<guest-slug>/relay-request.md` per the format and rationale in the guardrails section above. This is not optional or conditional on how thin the dossier is — a dossier with plenty of VERIFIED material still generates this file if any UNVERIFIED ≥7 fact remains, since the point is surfacing the highest-leverage next fetch, not just rescuing a weak run.

### Monitor — implemented (M5), scheduled

Weekly, unattended, across every dossier in `state: seed`. Full instructions in `references/monitor-prompt.md` — deliberately lighter than `sweep`: one or two targeted searches per dossier for what's new since its last update, not a full 8-Sweeper fan-out, still scored and verified through the same Scorer/Verifier as `sweep` (same rubric, same Verification cap, same career-payoff tag), but only what clears ≥7 and comes back VERIFIED gets appended to `dossier.md` — sub-bar or unverified finds are logged to `sources.md` only, since nobody reviews a Monitor pass by hand the way a `sweep` run gets reviewed.

**Scheduling mechanism.** This is a genuinely recurring task, not a build-time script, so it's registered as a real scheduled Routine in this environment (Claude Code Remote's trigger system — `create_trigger`/`list_triggers`/`update_trigger`), not an in-process cron hand-rolled inside the skill (which wouldn't survive past this session and was never a real option). The Routine fires weekly with `create_new_session_on_fire: true` — each firing gets a completely fresh session in this same environment, since a Monitor run shouldn't depend on any particular interactive session still being alive a week later. Its stored prompt is a standalone instruction (check out the right branch, read this file and `references/monitor-prompt.md`, find every `Guests/*/dossier.md` with `state: seed`, run the Monitor procedure per dossier, commit and push anything that changed). Check `list_triggers` for the current schedule and last-run status rather than assuming this doc is current — the cadence can be changed with `update_trigger` without touching this file.

## Roadmap (from the build plan, for context — not part of this skill's current behavior)

~~M2 sweep/score/verify~~ (done — see `sweep <name>` above) → ~~M3 people/outreach/artifacts~~ (done — see `sweep <name>` steps 6–8 above) → ~~M4 brief + question architect~~ (done — see `brief <name>` above) → ~~M5 scheduled weekly monitor~~ (done — see "Monitor" above) → ~~M6 referral-loop seeding~~ (done — see `refer <name> --from <slug>` above; not wired to an automatic post-episode trigger yet since no episode has ever been recorded — it's a callable command, not yet an automatic hook). M1–M6 are all built as of 2026-09-13.
