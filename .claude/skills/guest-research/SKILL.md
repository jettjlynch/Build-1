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

These apply to every mode below, including ones not yet built.

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

### `sweep <name>` — implemented (M2)

Grows an existing dossier using the actual Sweeper → Scorer → Verifier pipeline from the build plan (§2), instead of hand research. This is what "standing files that never stop growing" turns into once it's automated — later (M5) it's the same pipeline a scheduled monitor runs weekly, unattended, across every seed-state dossier. Requires a dossier already created by `seed`.

Full prompt text for every subagent below lives in `references/` — `sweeper-prompts.md`, `scorer-prompt.md`, `verifier-prompt.md`. Don't inline a paraphrase of them elsewhere in the skill; edit those files and every mode that uses them stays in sync.

1. **Sweep.** Dispatch all eight Sweeper agents from `references/sweeper-prompts.md` in parallel, one per source type (a–h). Each is scoped to exactly one source type and forced to justify why what it found isn't just a generic search's first page — if a Sweeper's own notes admit it fell back to a generic search, that's reported honestly, not hidden. Each returns raw, unscored facts with source and fetch date. A Sweeper reporting "nothing found for this source type" is a valid, honest result — never pad its output to look more productive than it was.
2. **Score.** Pool every Sweeper's raw output and run it once through the Scorer agent (`references/scorer-prompt.md`). The Scorer dedupes overlapping facts (merging sources when two Sweepers independently found the same thing — that's a real Verification signal, not noise), scores every surviving fact on all four axes with reasoning shown, applies the two hard rules (rejecting outright, not just scoring low, anything that fails one), and sorts by total. Nothing is called "verified" at this stage — the Scorer marks ≥7 and ≥10 as *pending* verification.
3. **Verify.** Every fact the Scorer marked ≥7 goes to the Verifier agent (`references/verifier-prompt.md`), one adversarial pass whose job is to try to break each fact: find independent corroboration or fail to, catch a contradiction, catch an aggregator source posing as multiple independent ones, re-check both hard rules independently. Each fact comes out VERIFIED, UNVERIFIED, or REJECTED. Only VERIFIED facts are usable in a future brief; UNVERIFIED facts stay in the dossier as a wall, not a warning, per the guardrails.
4. **Write back.** Append the pass to `dossier.md` under a new dated `## Sweep pass — <date>` heading (never overwrite or renumber prior entries — the dossier is a standing, cumulative file), sorted by score, each fact showing its full per-axis breakdown, its Sweeper source-type origin, and its verification verdict. Update `sources.md` with everything consulted, including Sweepers that came up empty.
5. Report honestly on the run: the highest-scoring fact with its full axis breakdown (not just the total), whether Obscurity actually hit 2 or 3 anywhere, and — if it didn't — which Sweeper(s) underperformed and why, the same way the M1 hand-research pass was assessed. A sweep that quietly matches M1's Obscurity ceiling is a failed test of the pipeline, not a neutral result; say so if it happens.
6. **People-finder (M3, implemented).** Run `references/people-finder-prompt.md` against the current dossier. Identifies 5–10 named, reachable pre-fame contacts, each tied to a specific dossier fact with a specific ask — never an invented name for a category. Appends to `people.md`; never duplicates an entry already there.
7. **Outreach drafter (M3, implemented).** Run `references/outreach-drafter-prompt.md` against the People-finder's output. One drafted email per person into `outreach/`, following the build plan §3 template, sender identity honest (Jett's own name by default, per build plan §6). **Never claims a booking or date that doesn't exist** — if the dossier is still in `seed` state, the email says so rather than reusing the template's "I'm interviewing them on [date]" language as fact. Drafts only; nothing here is ever sent by the skill.
8. **Artifact hunter (M3, implemented).** Run `references/artifact-hunter-prompt.md` against the dossier. Proposes 3–5 gift candidates ranked by Specificity, each tied to a named dossier fact — a candidate that would suit any successful person in the guest's field, not this guest specifically, doesn't belong on the list, and the hunter should say so plainly about its own weaker candidates rather than oversell them. Writes to `artifacts.md`. Nothing is purchased or committed to.

### `brief <name>` — partially implemented

Runs once a booking is confirmed. Per the build plan (§2–§3): reuses the same Sweeper/Scorer/Verifier trio and the People-finder/Outreach drafter/Artifact hunter trio, both now implemented under `sweep` above, then adds a Question architect, fanning out in parallel where a phase doesn't depend on another's output. It produces `prep-brief.md`: guest in one line, the hero fact + its artifact + its source, five supporting facts in arc order with scores, the referral chain, contacted people and what they said, sequenced questions, and a "do not use" list of high-scoring facts cut on the hard rules. Only the Question-architect phase and the final `prep-brief.md` assembly remain unbuilt (M4) — `brief` should currently run `sweep`'s full pipeline (steps 1–8 above) and then explain that the brief itself isn't assembled yet, rather than attempt a partial `prep-brief.md`.

## Roadmap (from the build plan, for context — not part of this skill's current behavior)

~~M2 sweep/score/verify~~ (done — see `sweep <name>` above) → ~~M3 people/outreach/artifacts~~ (done — see `sweep <name>` steps 6–8 above) → M4 brief + question architect → M5 scheduled weekly monitor across all seed-state dossiers (reuses `sweep`'s pipeline unattended) → M6 automatic referral-loop seeding. Each milestone is a separate build pass; don't reach ahead of the milestone this skill is actually at.
