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
4. **Write back.** Append the pass to `dossier.md` under a new dated `## Sweep pass — <date>` heading (never overwrite or renumber prior entries — the dossier is a standing, cumulative file), sorted by score, each fact showing its full per-axis breakdown, its Sweeper source-type origin, and its verification verdict. Update `sources.md` with everything consulted, including Sweepers that came up empty. `people.md`, `artifacts.md`, and `outreach/` are untouched — those are M3.
5. Report honestly on the run: the highest-scoring fact with its full axis breakdown (not just the total), whether Obscurity actually hit 2 or 3 anywhere, and — if it didn't — which Sweeper(s) underperformed and why, the same way the M1 hand-research pass was assessed. A sweep that quietly matches M1's Obscurity ceiling is a failed test of the pipeline, not a neutral result; say so if it happens.

### `brief <name>` — described, not implemented

Runs once a booking is confirmed. Per the build plan (§2–§3), this reuses the same Sweeper/Scorer/Verifier trio from `sweep`, then adds a People-finder, an Outreach drafter, an Artifact hunter, and a Question architect, in that rough order, fanning out in parallel where a phase doesn't depend on another's output. It produces `prep-brief.md`: guest in one line, the hero fact + its artifact + its source, five supporting facts in arc order with scores, the referral chain, contacted people and what they said, sequenced questions, and a "do not use" list of high-scoring facts cut on the hard rules. The People-finder/Outreach/Artifact-hunter/Question-architect phases aren't built yet — `brief` should currently just explain this and exit rather than attempt a partial run.

## Roadmap (from the build plan, for context — not part of this skill's current behavior)

~~M2 sweep/score/verify~~ (done — see `sweep <name>` above) → M3 people/outreach/artifacts → M4 brief + question architect → M5 scheduled weekly monitor across all seed-state dossiers (reuses `sweep`'s pipeline unattended) → M6 automatic referral-loop seeding. Each milestone is a separate build pass; don't reach ahead of the milestone this skill is actually at.
