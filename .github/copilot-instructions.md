# Instructions for this repository

This file is the single source of truth for AI coding agents working here. `CLAUDE.md` imports it; do not duplicate guidance there.

## Repository shape

`leissa/leissa` — the GitHub *profile* repository of Roland Leißa. `README.md` is rendered as the profile page at <https://github.com/leissa>, so it is the whole product, not documentation for something else. This is a publication archive, not an application or library.

Content surfaces:

- `README.md`: bio and social badges, then the canonical publication index under `## 📖 Publications`, one `###` section per year, newest first.
- `pdf/`: the paper PDFs linked from the README.
- `images/`: photos and logos, *not* used by `README.md` — see below.
- `github-metrics.svg`, `github-metrics-languages.svg`: the two GitHub stats cards in the bio, **generated** by `.github/workflows/metrics.yml` and committed by it. Never hand-edit them, and do not "fix" them if the badges 404 — that only means the workflow has not run yet (trigger it manually) or the `METRICS_TOKEN` secret is missing or expired.

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

- Badge order in use: venue → PDF → publisher DOI → arXiv → Zenodo → award → GitHub artifact → YouTube → **dblp last**.
- Include only the badges that actually exist for a paper; copy the exact shields.io style of a neighbouring entry rather than inventing a new one. `style=flat-square` throughout; grey badges for PDF/YouTube/dblp, blue for venues and DOIs.
- Two publisher-badge idioms are in use, and each publisher sticks to one: **named label** (`ACM-<doi>`, `Springer-<doi>`, `Elsevier-<doi>`, `World_Scientific-<doi>`, `%E2%8C%82_UdS-<doi>`) and **logo-only label** for IEEE and Zenodo, where the left segment is empty and carries just the logo — `badge/-<doi>-blue?style=flat-square&logo=ieee&labelColor=555&logoSize=auto`. Keep the parameter order as written; the `[![IEEE]`/`[![zenodo]` alt text is what names the publisher, so it must match the DOI prefix (`10.1109` → IEEE, `10.1007` → Springer, `10.1145` → ACM, `10.5281` → Zenodo).
- Spell the DOI out in the badge label and use the **same** DOI in the label and the href — a label saying one DOI while the link goes somewhere else is the single most common defect here. Write `-` in DOIs as `--` (shields escapes it) and a literal `/`, not `%2F`.
- All DOI links go through `https://doi.org/<doi>` — not `dx.doi.org`, not `dl.acm.org/doi/abs/...`.
- dblp badge URLs must be the record for *this* paper (`?view=bibtex`). Do not hand-guess the key from author initials; dblp's own numbering and homonym digits make that unreliable. Fetch <https://dblp.org/pid/05/10957.xml> — it lists the authoritative `key` and `ee` (DOI) for every publication, and is the fastest way to verify a whole batch of entries.
- Awards are badges too, with an emoji in the label (e.g. `%F0%9F%A5%88_HiPEAC-Best_Paper_Award`).
- Venue badges link to the venue for *that* year (conference site, or the publisher's volume/proceedings TOC). Copy-pasting an entry tends to leave a link pointing at the wrong year's site.
