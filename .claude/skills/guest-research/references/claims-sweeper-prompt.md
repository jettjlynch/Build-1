# Claims Sweeper — M7a, added 2026-09-15

## Why this exists

A coded cross-analysis of all 22 TWR episodes against 9 benchmark shows found that "push-past" questioning — citing a guest's own prior claim to challenge or go beyond the rehearsed version, Steven Bartlett's core technique, backed by DOAC's own 20-30 page prior-claims document per guest — occurred exactly once in 22 episodes. That is the single most automatable gap the analysis found, and this skill already has the machinery for it: a rubric, a Verification cap, a quarantine discipline, and a relay-request pipeline. This Sweeper is that machinery pointed at a new kind of target.

## What a "claim" is, and how it's different from a fact

Every other Sweeper (a–h) targets **what happened to the guest** — documented events, records, other people's accounts. This Sweeper targets **what the guest has SAID about themselves**, elsewhere, in their own words: other podcast appearances, their own book(s) or memoir, interviews, keynotes, panels. A claim is a first-person assertion the guest has made about their own experience, motivation, a decision, or their own characterization of an event — not a bare biographical fact.

A claim and a fact can be about the same underlying event and both belong in the dossier — they are not competing categories. Example: "Bellamy graduated Rutgers in 1989" (a fact, sourced to Rutgers' own news office) and "I always felt like an outsider at Rutgers" (a claim, sourced to Bellamy's own words in an interview) can both be true and both worth recording; they serve different purposes. The fact sweepers ask "did this happen and can it be corroborated." This sweeper asks "what did the guest say about it, and does that telling hold up against itself or against what's known."

**This is not a rerun of Sweeper (a).** Sweeper (a) gathers raw biographical facts from podcast/interview transcripts. This sweeper specifically extracts the guest's own quoted claims and actively looks for two things Sweeper (a) doesn't: repetition-with-variation across multiple tellings (a "rehearsed" story that has drifted, been softened, or been told with different emphasis at different times — prime push-past material), and direct tension against another claim or against a dossier fact.

## Where to look

- Other podcast appearances (not just the flagship show this guest might eventually be booked on — every appearance findable).
- The guest's own book(s), memoir, or ghostwritten work, if one exists (cross-reference with the mandatory own-book check already added to Sweeper (g) — if that check found a book, this sweeper should mine it for claims, not just facts).
- Interviews (print, video, any outlet).
- Keynotes, panels, commencement addresses, awards-acceptance speeches — anywhere the guest has spoken about themselves at length in their own words.

## Verbatim-first discipline

Record the claim as close to verbatim as the source allows. If a direct quote is available, use it in quotation marks. If only a paraphrase is available (a listicle or article characterizing what the guest said, rather than quoting it), record it as a paraphrase and say so explicitly — **never present a paraphrased characterization as if it were the guest's own words.** This is the same discipline that caught a false attribution earlier in this skill's history (Nick Khan's Filmhounds/Variety misattribution) and it applies here with even more force, since the entire value of a claim is that it's the guest's own words to push against.

## No invention — quarantine, don't guess

If a claim can't be sourced to a specific appearance, date, or at minimum a specific outlet's characterization of one, it does not go in `claims.md` at all — not even as a low-scored entry. This differs from the fact sweepers, which do record low-scoring facts as data points; a claim with no real source isn't a data point, it's a rumor about what someone said, and inventing or half-sourcing one is worse than leaving the gap open. A Sweeper reporting "searched the guest's known podcast appearances for claims about X, found none quotable" is a valid, honest result — log it in `sources.md`, not `claims.md`.

## The cross-check step (this is the actual point of the exercise)

For every claim found, check it against two things:

1. **Other claims found in this same sweep.** Has the guest told this story, or made this specific assertion, differently elsewhere — a different number, a different motivation, a different sequence of events, a softened or sharpened version? Two tellings that are simply consistent are not a tension flag. Two tellings that diverge on a specific, checkable detail are.
2. **Existing dossier facts.** Does this claim conflict with, complicate, or go further than something already established (VERIFIED or UNVERIFIED) in `dossier.md`? A claim that contradicts a VERIFIED fact is a genuine, high-value tension flag. A claim that contradicts an UNVERIFIED fact is still worth flagging, but say plainly that neither side is confirmed.

Run this cross-check before scoring — a tension flag changes whether a claim clears the usability bar (see below), so it has to be checked first, not as an afterthought.

## The reduced rubric — Specificity and Verification only

Obscurity and Era do not apply to claims. This is not the same as scoring them 0 — they are genuinely not applicable axes for a statement someone made, and `claims.md` should say "N/A" on both, not a numeric zero, so nobody later mistakes an unscored axis for a scored-and-failed one.

**Specificity (0–3), same definition as the fact rubric:** 0 = a vague category ("I've always been an outsider"). 1 = a named thing ("I felt like an outsider at Rutgers"). 2 = a named thing plus a date/place ("I felt like an outsider at Rutgers freshman year"). 3 = a single moment with emotional charge (a specific scene, a specific line said to or by someone, a specific decision point).

**Verification (0–3), same definition and same cap as the fact rubric:** 0 = single unverified source (the claim as reported by one outlet, unconfirmed). 1 = single reputable source. 2 = two independent sources reporting the same claim consistently. 3 = a primary artifact exists — the guest's own words directly fetched and read (a transcript, the book itself, a directly-fetched interview page), not a search-snippet paraphrase. **The same Verification cap applies: no claim may score 3 unless its source has actually been directly fetched and read by an agent.** If that hasn't happened, cap at 2. A claim needing a fetch to clear this cap, or to resolve a tension flag, goes into `relay-request.md` exactly like a fact does — see below.

## Usability threshold — different shape from the fact rubric, on purpose

Facts use an additive threshold (sum of four axes ≥7, hero ≥10). Claims use only two axes, so a sum-based threshold doesn't carry the same meaning. Instead:

**A claim is usable (a genuine push-past candidate) if: Specificity ≥2 AND (a tension flag is present OR Verification ≥2).**

Read this as: a claim has to actually say something specific enough to push on (Specificity ≥2), and it either has to be in tension with something else (which is itself the reason to push on it, regardless of how well-corroborated it is) or be well-corroborated enough that citing it back to the guest is safe even without a live tension (Verification ≥2). A vague, well-corroborated claim isn't useful (nothing to push on). A specific, tension-flagged claim from one source is exactly the interesting case even at Verification 0–1 — that's the whole point of push-past questioning, and it should be logged as usable with its verification level stated plainly, not held to the same bar a hero fact needs.

Any claim that fails this threshold is still recorded in `claims.md` (never dropped), just marked not usable — same "hold everything found" discipline as the fact dossier.

## Hard rules still apply

Both hard rules from the Nardwuar Bar apply unchanged: a claim must be public or consensually given (a claim the guest made in a private conversation that later leaked without consent is cut, however good it looks), and a claim cannot be used if its substance is actually about a private individual's personal life who isn't the guest (a claim in which the guest discusses, say, an ex-partner's private medical history is cut on that basis, even if the guest is the one who said it). These override score exactly as they do for facts.

## `claims.md` schema

```
# Claims — <Guest Name>

## Claim #1
**Claim:** "<verbatim quote>" — or, if not verbatim: [paraphrase, not a direct quote] <paraphrased content>
**Source:** <podcast/book/interview/keynote name>, <date>. <Fetched directly | search-snippet only>.
**Specificity:** 0–3, with one line of reasoning against the definition above.
**Verification:** 0–3 (capped at 2 without a direct fetch), with one line of reasoning.
**Tension flag:** None | vs. Claim #N (this same file) | vs. dossier.md fact #N — one line on the actual nature of the tension, not just "these differ."
**Usable (push-past candidate):** Yes/No, per the Specificity ≥2 AND (tension OR Verification ≥2) rule above.
**Hard rules:** Pass/Cut, with reasoning if cut.
```

Claims that fail a hard rule are omitted entirely from usable status but still logged with the cut reasoning shown, same as facts — never silently dropped, never presented as usable.

## Relay-request integration

A claim that needs a fetch — either to clear the Verification cap or to resolve a live tension flag — is a legitimate `relay-request.md` candidate and competes for the same 2-to-4-item cap as fact-driven items, ranked by the same leverage principle (how much one fetch would move this, weighed against how reachable it is). Label it clearly as a claim, not a fact, in the relay-request entry (e.g. "Claim #3 — ..." instead of "Fact #N — ...") so whoever's fetching knows which file to update the result in.

## Feeding into the brief (see `question-architect-prompt.md` / `SKILL.md`'s `brief` mode)

Usable, tension-flagged claims are the raw material for `prep-brief.md`'s "Push-past candidates" section. That section is built by whichever step assembles the brief (currently the Question architect) — this Sweeper's job stops at producing a clean, honestly-scored `claims.md`; it does not draft the challenge questions itself.
