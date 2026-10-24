# Big If True, script-free edition

This is a fork of [Verso-Lab/big-if-true-plugin](https://github.com/Verso-Lab/big-if-true-plugin),
the fact-checking skill by [Verso](https://verso.ink). The method, the
scripts and the checks are Verso's and unchanged. One thing is different:
**the report page contains no JavaScript.**

Some AI workspaces, Langdock among them, block scripts in HTML pages for
security reasons. The original report page builds itself with JavaScript,
so there it stays empty. In this edition `scripts/assemble.py` writes the
whole page (article, highlights, claim notes, claims list, exports) as
plain HTML, and the interactive parts run on HTML and CSS alone:

- clicking a highlighted passage opens its claim notes (a link to the note)
- the verdict filters and the Article / Claims switch are radio buttons
- the Export section shows Markdown, CSV and JSON in text boxes to copy,
  plus download links where the viewer allows them
- the copy buttons are gone, since copying to the clipboard needs a script

The same page still works in Claude, Codex or any browser.

**Install in Langdock:** upload the folder `skills/big-if-true` as a skill
(zipped, if Langdock asks for a file). The report page needs code
execution; without it the skill falls back to a text report.

Licence: MIT, like the original. All credit for the method goes to Verso.

---

## The original README

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
