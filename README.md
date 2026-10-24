# Big If True, Langdock-ready edition

This is a fork of [Verso-Lab/big-if-true-plugin](https://github.com/Verso-Lab/big-if-true-plugin),
the fact-checking skill by [Verso](https://verso.ink/), adapted by
Ole Reissmann for [THEFUTURE](https://olereissmann.com/newsletter/), a
newsletter on all things AI and journalism. The fact-checking method is
Verso's. This edition makes two important changes to it.

## The two important changes

### 1. The report page works in Langdock

Some AI workspaces, Langdock among them, block JavaScript in HTML pages
for security reasons. The original report page builds itself with
JavaScript, so in those workspaces it stays empty.

Here `scripts/assemble.py` writes the whole page as finished HTML, with
no JavaScript and no interactive CSS. The only interaction is following
links:

- every checked passage in the article is highlighted and links, like
  its footnote number, to that claim's verdict, rationale and sources in
  a numbered list under the article
- each entry in that list links back to its passage
- the colored bar at the top and the "Needs review" line link straight
  to the claims
- the Export section at the bottom shows Markdown, CSV and JSON in text
  boxes to copy, plus download links where the viewer allows them
- gone, because they need a script or interactive CSS: the verdict
  filters, the Article / Claims switch, the pop-up notes, the copy
  buttons and the hover effects
- the page stays readable even if a viewer strips its styles

The page still works in Claude, Codex or any browser.

### 2. Safeguards against copies and news-agency stories

When the checked text is a news-agency story (dpa, Reuters, AFP, AP and
others) or built on one, a web search mostly finds the same story again:
the text itself at another address, or the same agency copy run by other
outlets, word for word or lightly rewritten. The original method already
said that such copies prove nothing, but gave no way to spot them. This
edition adds four safeguards:

- **The agency basis is recorded first.** Pass 1 starts `claims.md` with a
  `wire:` line (`wire: dpa`, `wire: none`), and `assemble.py density`
  prints any agency markers it finds in the text, such as "Berlin (dpa) -",
  "lb/dpa" or "mit Material von".
- **Agency copy sends the check to the primary source.** If the text is
  agency copy, the agency and every outlet carrying the story count as
  the checked text itself. Those claims are checked against what the
  agency relied on: the press release, the statistic, the authority, the
  court, the recording.
- **A copy detector.** `scripts/evidence.py overlap source.txt page.txt`
  compares a page with the checked text, leaves quotations out (two
  outlets may honestly quote the same words) and reports COPY, SAME
  BASIS or OWN WORDING. Every outlet is tested before it can carry a
  verdict.
- **A stricter bar for "supported".** Two outlets count as two sources
  only if both show their own reporting. Two rewrites of one agency story
  are one source; if that is all there is, the claim stays "could not
  verify", and the rationale says so.

These are instructions to the model plus one mechanical check; a
heavily rewritten version can still slip past the detector.

## Smaller changes

- **`SKILL.md`** describes the footnote page and the Export section, and
  has a shorter description (under 250 characters).
- **The footer** credits this edition next to Verso.
- **Document comments** say "posted by an AI assistant" instead of "posted
  by Claude", since Langdock may run other models.

## Install in Langdock

Zip the *contents* of `skills/big-if-true`, so that `SKILL.md` sits at the
root of the archive next to `assets/`, `references/` and `scripts/` (not
inside a parent folder), and import the zip as a skill. The report page
needs code execution; without it the skill writes a text report instead.

## Questions and bugs

Problems with this edition: open an issue in this repository, not with
Verso. Problems with the method itself are best reported to Verso.

Licence: MIT, like the original. All credit for the method goes to Verso.

---

## The original README

*Below is Verso's README, unchanged. Its install links set up the
original version, not this one.*

A fact-checking plugin for Claude by [Verso](https://verso.ink). Give it an
article, a draft, a newsletter or a press release: it finds every checkable
claim, verifies each against primary records and the live web, and delivers
an annotated report where every claim carries a verdict and its sources.

- **Supported**: two independent sources agree, or one definitive source settles it.
- **Couldn't verify**: the evidence doesn't settle it either way.
- **Needs review**: something could be off here. Have a look.

More, including how we tested it: [verso.ink/big-if-true](https://verso.ink/big-if-true).

## Install

**Claude app and Cowork**: find Big If True in the plugin directory under
Customize → Plugins → Discover, or
[add it from this repository](https://claude.ai/customize/plugins/new?marketplace=Verso-Lab/big-if-true-plugin&plugin=big-if-true):
Sync, then Add next to Big If True.

**Claude Code**: add this repository as a marketplace:

```
/plugin marketplace add Verso-Lab/big-if-true-plugin
/plugin install big-if-true@verso
```

Then ask Claude to fact-check something. The plugin needs web search; with
code execution it builds the report page and runs the official-data lookups.

## What it connects to

Big If True needs no accounts or API keys and keeps no data of its own. To
check a text, Claude searches the web and opens the pages the evidence is
on, in the browser when the session has one. It writes its working files and the report page in the session's own
folder. The bundled scripts in `skills/big-if-true/scripts/` only read
public records, and each call sends only the search term, identifier or
data series it looks up:

- `evidence.py`: Internet Archive (archive.org, web.archive.org), Crossref,
  PubMed (NCBI), arXiv, SEC EDGAR, Wikidata and CourtListener.
- `marketdata.py`: FRED, US Treasury (home.treasury.gov and Fiscal Data),
  Yahoo Finance, Bank of England, Bundesbank, ECB, Eurostat, OECD,
  Frankfurter and the US EIA.
- `assemble.py` builds the report page locally and makes no network calls.

## What's in here

`skills/big-if-true/` is the skill: the method (`SKILL.md`), the claim
extraction rules and per-topic evidence routes (`references/`), the report
builder and data lookups (`scripts/`), and the report page (`assets/`).

This repository is published from Verso's development repository at each
release; changes are made there and measured against a test set before they
ship. Feedback and bug reports: [team@verso.ink](mailto:team@verso.ink) or an
issue here.

## Licence

MIT, © 2026 Verso Lab LLC. See [LICENSE](LICENSE).
