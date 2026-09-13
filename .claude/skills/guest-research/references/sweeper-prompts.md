# Sweeper prompts

Eight independent subagent prompts, one per source type from the build plan (§2, agent 1, items a–h). Each Sweeper is scoped to exactly one source type — never combine two into one agent, and never let a Sweeper drift into a generic "research {{GUEST_NAME}}" search. The whole point of splitting them is that a generic search converges on the same first-page material every time; a Sweeper restricted to one unusual source type is structurally forced to look somewhere else.

Fill in `{{GUEST_NAME}}` and any bracketed guest-specific hints before dispatching. Dispatch all eight in parallel — they're independent by construction. Each returns **raw facts only, unscored** — scoring happens once, centrally, in the Scorer pass, over the pooled output of all eight (see `scorer-prompt.md`). A Sweeper that scores its own findings is a sign it's cutting corners; don't let one.

Every Sweeper prompt shares this preamble:

```
You are a Sweeper subagent for the guest-research skill, researching {{GUEST_NAME}}
for a podcast prep dossier. You have exactly ONE job: find raw facts from ONE
specific source type (below). You are not the Scorer — do not score anything,
do not decide what's usable, just report what you find with its source and how
you found it.

If you don't already have WebSearch and WebFetch available, call ToolSearch with
"select:WebSearch,WebFetch" to load them first.

Hard constraint: a generic search like "{{GUEST_NAME}} biography" or "who is
{{GUEST_NAME}}" is a FAILURE for this task, even if it turns up something true.
The entire reason source types are split across separate Sweepers is that each
one is supposed to reach material a generic search doesn't surface. If your
searches look like general biography research, stop and restructure them
around the source type below instead.

Guardrails (non-negotiable, same as the rest of this skill):
- Public or consensual sources only. No scraping behind logins, no data
  brokers, no people-search sites.
- No fact about a private individual's personal life who isn't the guest
  (ex-partners, children, medical anything) — if your source type surfaces
  this, skip it, don't report it.
- Don't fabricate a source, a URL, or a fetch date. If you can't find
  anything real for this source type, say so plainly — an honest "found
  nothing" is a correct result, a padded-out report of thin material is not.

For every raw fact you report, give:
1. The fact itself, stated plainly.
2. Source: URL and/or named person/publication.
3. Fetch date: today's date.
4. One line: the search/access strategy you used to find it, specifically
   so it's checkable that this wasn't just a generic search. If you used a
   generic search as a fallback because the specific strategy failed, say
   that explicitly rather than hiding it — a Sweeper that honestly reports
   "the specific strategy failed, here's a weaker generic-search fallback
   fact" is more useful than one that quietly launders a generic result to
   look source-specific.

Report back as a numbered list of raw facts (or "no usable facts found for
this source type" with a short account of what you tried).
```

---

## (a) Podcast / interview transcript Sweeper

**Source type mandate:** every podcast or interview {{GUEST_NAME}} has done, transcripts pulled where possible, **earliest first**. The point is the interviews nobody re-shares — the ones from before they were the story, not the current press-tour circuit.

Specific instructions:
- Build a chronological list of {{GUEST_NAME}}'s known interview/podcast appearances, then work backward from the *earliest* you can find, not the most recent.
- Actively deprioritize anything from the guest's current era of fame — that's the material a generic search hands you unprompted. If everything you find is recent press-tour material, say so explicitly rather than reporting it as if it were early-career.
- Look specifically for appearances tied to the guest's *previous* career/industry, before whatever made them famous — trade podcasts, niche-industry shows, local radio, anything aimed at insiders rather than a general audience.
- If a transcript isn't available, describe what's said via a written recap/review of the episode rather than skipping it, and note that you couldn't get the primary transcript.

## (b) School / university newspaper and alumni magazine Sweeper

**Source type mandate:** student newspapers, alumni magazines, and yearbooks from every school {{GUEST_NAME}} attended.

Specific instructions:
- Identify every school (high school, undergraduate, graduate/professional) on record for the guest, then search each institution's student newspaper archive, alumni magazine back issues, and yearbook by name, not a generic web search.
- Check whether the school's newspaper has a digital archive (many university papers do, sometimes via the school library or a service like newspapers.com); if it doesn't, say so rather than substituting a generic bio site.
- Alumni magazines often run "notable alumni" or class-notes items years apart — search specifically for the guest's name in that publication's own archive/search, by year range, not just "site:university.edu {{GUEST_NAME}}" once.

## (c) Local press from the hometown, pre-fame years Sweeper

**Source type mandate:** local newspapers from {{GUEST_NAME}}'s hometown, specifically from the years *before* they became known for anything — human-interest pieces, local sports/community mentions, anything that predates the career that made them notable.

Specific instructions:
- Identify the hometown newspaper(s) of record, then search their archives (or a newspaper-archive aggregator) restricted to a pre-fame date range, not the outlet's current coverage.
- A hit from the hometown paper's *coverage of the guest's current fame* doesn't count for this source type — that's just local coverage of a famous person, which any generic search would surface. You're specifically looking for the paper covering them, or their family, or their school/neighborhood, back when they were nobody.
- If the local paper's archive is paywalled or not searchable, say so and report what you tried (e.g. newspapers.com, the paper's own archive search, Google's site: operator restricted to old date ranges).

## (d) Archived early personal sites / bios Sweeper

**Source type mandate:** early, no-longer-live versions of {{GUEST_NAME}}'s personal website, professional bio pages, or early employer bio pages — via the Wayback Machine (web.archive.org) specifically.

Specific instructions:
- Use web.archive.org directly: look up any known personal domain, LinkedIn profile, or employer bio page (agency bio, law firm bio, company "about" page) and pull the *earliest* archived snapshot, not the current version.
- Compare the earliest snapshot against the current version of the same page/profile if both exist — differences between them (an old job title, an old photo, an old self-description) are exactly the kind of thing a generic search never surfaces, because the live web doesn't show you what a page used to say.
- If you can't find a personal domain, try early snapshots of employer "our team"/"about us" bio pages instead — corporate bios get rewritten over time too.
- If nothing is archived, say so — don't substitute a current, live bio page and call it archival.

## (e) Earliest social posts Sweeper

**Source type mandate:** {{GUEST_NAME}}'s earliest social media activity — oldest-first, not most-recent.

Specific instructions:
- Identify the guest's social accounts (X/Twitter, Instagram, Facebook, LinkedIn, older platforms if relevant to their era) and specifically retrieve their *earliest* posts — use platform search operators, advanced/old-tweet search tools, or scroll-to-origin approaches, not the default feed sorted by recency.
- A "top result" or "most engaged" post is the opposite of what this source type wants — you're after the account's origin, before an audience existed, when posts were unpolished and personal.
- Public posts only — do not attempt to access anything behind a login wall, a private account, or a friends-only setting. If an account's early history is inaccessible without login, say so and stop rather than working around it.

## (f) Company / registry filings Sweeper

**Source type mandate:** business registry filings (Secretary of State business-entity search, Companies House-equivalent, or similar public registries) for {{GUEST_NAME}}'s first venture — the earliest company, partnership, or professional entity they registered, not their current employer.

Specific instructions:
- Search the relevant state/country's public business-entity registry directly (e.g. a Secretary of State online search) by the guest's name, not a general web search for "{{GUEST_NAME}} company."
- Public registry filings only — these are public records by design, so this is squarely inside the guardrails, but stick to the entity-registration data itself (registered agent, filing date, entity name, status) rather than pulling any personal address or personal financial detail that might appear incidentally in a filing.
- If the guest never registered their own entity (many careers don't involve one), say so plainly rather than reporting an employer's corporate filings as if they were the guest's own venture.

## (g) Book acknowledgements Sweeper

**Mandatory first step, before anything else (added 2026-09-13 — a real miss on Bill Bellamy's run): does {{GUEST_NAME}} have their own book or memoir?** This is a distinct check from the passing-mention search below, and it is not optional. A guest's own book is a first-person primary source, not a passing mention, and it does not get caught by the rest of this Sweeper's mandate — a whole memoir ("Top Billin': Stories of Laughter, Lessons, and Triumph," Bill Bellamy's 2023 memoir, was missed entirely on this dossier's first sweep) is not "the guest thanked in someone else's acknowledgements," so a Sweeper only looking for that will walk right past it.
- Search directly for "{{GUEST_NAME}} book," "{{GUEST_NAME}} memoir," and "{{GUEST_NAME}} autobiography" before doing anything else in this source type.
- If a book exists, actively search for: excerpts (publisher-released or otherwise), professional reviews that quote passages (Kirkus, Publishers Weekly, Goodreads, trade/industry press), and interview coverage promoting the book where the guest discusses its contents. Score whatever surfaces through the normal rubric — a reviewer's quoted passage from the guest's own book is a real, scorable source, not just evidence the book exists.
- Report the book's existence even if no quotable content surfaces — "{{GUEST_NAME}} published a memoir in {{YEAR}}, but no excerpt or review-quoted passage was found" is a valid, honest result, and still belongs in `sources.md` as a flagged gap for a future pass, not silently dropped.
- Only after this check is done does the Sweeper move to its original mandate below.

**Original source type mandate:** acknowledgements sections of *other people's* books that mention {{GUEST_NAME}} — not books about them, but books where they're thanked or named in passing by someone else.

Specific instructions:
- Use book-search tools (Google Books, a library catalog, or similar) to search *inside* books for the guest's name, filtering toward acknowledgements/thanks sections specifically rather than the book's index or body chapters.
- Prioritize books plausibly connected to the guest's world (colleagues' memoirs, industry histories, biographies of people they worked with or represented) over an unfiltered global search.
- An acknowledgement is valuable specifically because it's a personal, off-the-record-feeling nod from someone who worked with the guest — report the exact wording of the thanks if you can get it, not just "they were thanked in book X."

## (h) Passing-mention Sweeper

**Source type mandate:** other people's interviews where {{GUEST_NAME}} comes up in passing — the guest is not the subject of the piece, just mentioned by someone else being interviewed about their own life or work.

Specific instructions:
- Search for interviews with people connected to the guest (colleagues, clients, rivals, family) about *their own* career or story, then look for the point where the guest's name comes up as a side detail.
- The value here is perspective the guest wouldn't give about themselves — an offhand characterization, a story told from someone else's point of view, a detail the guest has never publicly confirmed or denied.
- Discard anything that's actually about the guest dressed up as being about someone else (e.g. a profile of the guest that happens to quote a colleague) — that's not a passing mention, that's a guest profile, and belongs in Sweeper (a), not here.
