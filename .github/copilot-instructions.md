# Instructions for this repository

This file is the single source of truth for AI coding agents working here. `CLAUDE.md` imports it; do not duplicate guidance there.

## Repository shape

`leissa/leissa` — the GitHub *profile* repository of Roland Leißa. `README.md` is rendered as the profile page at <https://github.com/leissa>, so it is the whole product, not documentation for something else. This is a publication archive, not an application or library.

Content surfaces:

- `README.md`: bio and social badges, then the canonical publication index under `## 📖 Publications`, one `###` section per year, newest first.
- `pdf/`: the paper PDFs linked from the README.
- `images/`: photos and logos, *not* used by `README.md` — see below.

## Build, test, and lint commands

There are none: no source tree, package manifest, build system, lint configuration, or test suite. Changes are content edits. "Verifying" a change means reading the Markdown and confirming every linked file actually exists in the repo.

## Architecture: assets are addressed by absolute raw URLs

Nothing in this repo is linked relatively. PDFs are referenced as:

```
https://raw.githubusercontent.com/leissa/leissa/main/pdf/<slug>.pdf
```

Consequences:

- Content changes are coupled across two places — the file in `pdf/` and its `README.md` entry. Change one without the other and the badge 404s silently (GitHub renders a broken badge, not an error), or the publication disappears from the index.
- The URLs are pinned to `main`, so an asset only becomes reachable once pushed to `main`.
- `images/` is an asset host for the **sibling PLaC group website** in `../` (`/home/roland/plac/website`, a separate Makefile-driven project whose `pages/` embed `https://raw.githubusercontent.com/leissa/leissa/main/images/*.jpg`). Renaming or deleting anything in `images/` breaks that site, and grepping this repo alone will not reveal the reference.

## Conventions for publication entries

Keep sections year-based and newest-first. PDF slugs are author initials plus a two-digit year, in author order: `lg26.pdf` = Leißa, Griebler 2026; `lumh25.pdf` = Leißa, Ullrich, Meyer, Hack 2025; `bbhlmz13.pdf` = Braun, Buchwald, Hack, Leißa, Mallon, Zwinkau 2013.

Each entry is a bullet with `<br>`-terminated metadata lines followed by indented badge links:

```markdown
* **Paper Title** <br>
  First Author, Second Author, Roland Leißa <br>
  [![VENUE 20XX](https://img.shields.io/badge/VENUE-20XX-blue?style=flat-square)](https://venue.site)
  [![PDF](https://img.shields.io/badge/PDF-grey?style=flat-square&logo=readthedocs)](https://raw.githubusercontent.com/leissa/leissa/main/pdf/slug.pdf)
  [![ACM](https://img.shields.io/badge/ACM-10.1145/XXXXXXX-blue?style=flat-square&logo=acm)](https://doi.org/10.1145/XXXXXXX)
  [![dblp](https://img.shields.io/badge/dblp-grey?style=flat-square&logo=dblp)](https://dblp.uni-trier.de/rec/...html?view=bibtex)
```

- Badge order in use: venue → PDF → publisher DOI (ACM / IEEE / Springer / Elsevier / World Scientific / UdS, each with its own `logo=`) → arXiv → Zenodo → award → GitHub artifact → YouTube → **dblp last**.
- Include only the badges that actually exist for a paper; copy the exact shields.io style of a neighbouring entry rather than inventing a new one (`style=flat-square` throughout; grey badges for PDF/YouTube/dblp, blue for venues and DOIs). Do not switch to a different linking style unless the whole file is being normalized consistently.
- Publisher badges spell the DOI out in the badge label; arXiv and Zenodo badges use the `10.48550/...` and `10.5281/zenodo....` DOI forms linked through `doi.org`.
- dblp badge URLs must be the record for *this* paper (`?view=bibtex`). These are easy to leave stale when copy-pasting a previous entry — check the author/venue in the dblp key.
- Awards are badges too, with an emoji in the label (e.g. `%F0%9F%A5%88_HiPEAC-Best_Paper_Award`).
