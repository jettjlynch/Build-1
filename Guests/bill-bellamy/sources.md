# Sources — Bill Bellamy

Every URL/person/artifact consulted, regardless of whether it produced a usable fact. All 8 Sweepers ran 2026-09-13; every single source below was reached via WebSearch snippet only — **direct WebFetch was blocked by this environment's network egress policy for every domain attempted by every Sweeper and the Verifier**, including a trivial control case (`https://example.com`) the Verifier tried specifically to confirm this wasn't source-specific. No fetch-relay was used on this run (unlike Nick Khan's one-time manual assist).

## Used in scored facts

- rutgers.edu/news — "Bill Bellamy's Career in Comedy Started on Stage at Rutgers" — fact #1, VERIFIED
- New Jersey Monthly — "Bill Bellamy Reflects on Being 'The Rutgers Comedian' Before Big Break" — independent corroboration of fact #1, found by the Verifier
- Rutgers official Facebook post — further corroboration of fact #1
- VladTV, Drink Champs, 7PM in Brooklyn, CBS News New York — self-told Ray Romano/Rascals story — fact #2, UNVERIFIED (self-report only)
- Seton Hall Prep admissions Facebook post; Wikipedia "List of Seton Hall Preparatory School alumni" (independence unconfirmed) — fact #3, UNVERIFIED
- bizapedia.com, bizprofile.net — CA registry filing for "Bill Bellamy Entertainment, Inc." — fact #4, UNVERIFIED, HIGH-PRIORITY FLAG (same shape as the Nick Khan CA-Bar near-miss; more internally consistent this time, but identity link and primary record both unconfirmed)
- scholar.lib.vt.edu (Roanoke Times archive, Jan 22 1994) — fact #5, UNVERIFIED — quote attribution conflicts with a version found elsewhere credited to the LA Times; unresolved
- Encyclopedia.com bio entry; IMDb episode listings (cast credit unconfirmed) — fact #6, UNVERIFIED, corrected below the usability bar (single source dressed as two)
- baltimoresun.com archive (two 1994 URLs) — fact #7, UNVERIFIED
- X profile metadata (@BILLBELLAMY) — fact #8, UNVERIFIED, corrected below the usability bar (Obscurity was over-credited — join date is a public UI element)
- salon.com (May 2013, Carson Daly profile) — fact #9, UNVERIFIED
- Recent retrospective podcasts (VladTV, Drink Champs, etc.) — facts #10, #15, #16 (Uptown/Russell Simmons discovery, Wall Street job, Rutgers pageant story) — below usability bar
- 2paclegacy.net — fact #11 (MTV Jams/Tupac & Dr. Dre special) — below usability bar
- YouTube clip description — fact #12 (MTV/Michael Jackson interview) — below usability bar
- yellowscene.com — fact #13 (2008 Q&A, existence only) — below usability bar
- mcarchives.com — fact #14 (Last Call w/ Carson Daly) — below usability bar
- Publisher jacket copy, "Life and Def" (Russell Simmons memoir) — fact #17 — below usability bar
- Goodreads/Amazon/Kirkus listings — fact #18 (Kennedy's "The Kennedy Chronicles") — rejected as too thin

## Consulted, confirmed empty or structurally blocked

- **(c) Local press, pre-fame:** newspapers.com, genealogybank.com, nj.com, dailytargum.com — all blocked. Structural finding: Star-Ledger's own searchable archive only goes back to 1989, at the edge of his pre-fame years, not before them.
- **(d) Archive.org:** web.archive.org — hard-blocked ("Host not in allowlist," 403), confirmed again this run via direct curl through the proxy, matching every prior run of this skill.
- **(e) Social platforms:** X, Instagram, Facebook, archive.org, Socialblade, Nitter — all blocked for content retrieval; only account-creation metadata survived.
- **(g) Book acknowledgements:** Google Books, HathiTrust, Internet Archive full-text search — all blocked, same root cause as (d).
- **(f) look-alike entity, correctly excluded:** "Bill Bellamy Sons Inc" (inactive North Carolina corp) — unrelated Bellamy family business, not the comedian; found and correctly not attributed.
- **(b) archive-access finding, not a source gap in this dossier's favor:** Rutgers' digitized Daily Targum archive only covers up to ~1980, missing his actual mid-80s attendance window (in-person-only at Special Collections). Seton Hall Prep's student paper has no visible online historical archive either.

## Verifier's environment-constraint note

Before scoring anything, the Verifier confirmed WebFetch is non-functional for every domain in this session, including a neutral control case (`https://example.com`) — not site-specific gatekeeping. It also flagged that WebSearch's own "answer" synthesis is itself LLM-generated summarization of search results, not raw source text, and can smooth over or invent specifics the same way a confabulating source could — raw URLs/titles (evidence a page exists) were weighted more heavily than the tool's synthesized prose throughout this Verifier pass.

## Fetch-relay update (2026-09-13) — one source actually fetched and read

- **scholar.lib.vt.edu/VA-news/ROA-Times/issues/1994/rt9401/940122/01220359.htm** — fetched and read in full by Jett directly (not this session). Confirmed: N.F. Mendoza / Los Angeles Times piece, syndicated in the Roanoke Times, Jan 22 1994, Spectator section, page S-19. Resolves the Verifier's flagged LA Times/Roanoke Times contradiction as wire syndication. This session independently spot-checked via WebSearch before accepting the upgrade: confirmed N.F. Mendoza is a real, 20+-year LA Times entertainment journalist (consistent with the byline); confirmed the LA Times Syndicate was a real, operating wire service through the 1990s (consistent with the syndication mechanism). **Not independently confirmed:** the specific syndication arrangement between these two exact papers, or the article's full text beyond what was relayed — that narrower piece still rests on the relay. Verification=3, dossier.md fact #5.
- **Incidental, independent find (not part of the relay):** this session's own WebSearch, prompted by the relay's mention of a "college sorority male beauty pageant," independently turned up a more specific version of the same story — hosted by **Delta Sigma Theta** sorority, during Bellamy's junior year studying economics — from a source distinct from both the Roanoke Times piece and the original Sweeper's retrospective-podcast sourcing. Not yet folded into fact #16's score (flagged as open follow-up in dossier.md's closing assessment, not resolved unilaterally).
- **CA registry filing (fact #4) — re-checked by the same relay pass, left as scored.** Jett confirmed the bizapedia/bizprofile listing is real (not a confabulation) but could not reach the primary bizfileonline.sos.ca.gov record (robots-blocked, no direct entity-number match found). Per explicit instruction, this fact stays exactly as scored — UNVERIFIED, not upgraded, not rejected.
