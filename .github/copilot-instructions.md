# Instructions for this repository

This file is the single source of truth for AI coding agents working here. `CLAUDE.md` imports it; do not duplicate guidance there.

## Repository shape

`leissa/leissa` — the GitHub *profile* repository of Roland Leißa. `README.md` is rendered as the profile page at <https://github.com/leissa>, so it is the whole product, not documentation for something else. This is a publication archive, not an application or library.

Content surfaces:

- `publications.yaml`: **the source of truth for every publication.** One entry per paper; its header comment documents the fields.
- `tools/gen.py`: renders `publications.yaml` into the `## 📖 Publications` section of `README.md`, into `leissa.bib`, and into `CITATION.cff`.
- `README.md`: hand-written head (logo, social badges, bio, stats card, the `## 🚀 Projects` section), then the *generated* publication index between the `<!-- BEGIN PUBLICATIONS -->` / `<!-- END PUBLICATIONS -->` markers.
  The `## 🚀 Projects` section is hand-written and sits *before* `## 📖 Publications`, so the sibling PLaC site (which slices from that heading onwards) does not pick it up. Project icons are hot-linked from each project's own repo (`assets/logo*.png`), star counts are live shields.io badges, and the badges are *not* wrapped in links to `/stargazers` — GitHub answers that page with 404 for logged-out visitors and the weekly link check.
- `leissa.bib`, `CITATION.cff`: generated; `CITATION.cff` drives GitHub's “Cite this repository” button.
- `pdf/`: the paper PDFs linked from the README, one per `publications.yaml` entry.
- `images/`: photos and logos — the theme-aware logo in the README head, plus assets for the sibling site, see below.

## Build, test, and lint commands

```sh
tools/gen.py          # regenerate README.md, leissa.bib (only with --bib) and CITATION.cff
tools/gen.py --check  # fail if the generated files are out of sync (what CI runs)
tools/gen.py --bib    # additionally refetch leissa.bib from doi.org (needs network)
```

`tools/gen.py` needs PyYAML and nothing else. It validates as it goes: every entry needs its `pdf/<id>.pdf`, every `pdf/*.pdf` needs an entry, DOI prefixes must be known, `arxiv`/`zenodo` must carry the right DOI prefix.

Two GitHub Actions workflows guard the result: `check.yml` runs `--check` on every push and pull request, `links.yml` runs [lychee](https://lychee.cli.rs) weekly over `README.md`, `publications.yaml`, `leissa.bib` and `CITATION.cff` (configured in `lychee.toml`) because a dead link here fails *silently* — GitHub renders a broken badge, not an error.

**Never hand-edit anything between the publication markers in `README.md`, nor `leissa.bib` or `CITATION.cff`.** Edit `publications.yaml` and re-run `tools/gen.py`; CI rejects the drift otherwise. Badge *rendering* — shields escaping, publisher logos, badge order — lives in `tools/gen.py`, so a new publisher or badge kind is a change to that file, not to the Markdown.

## Architecture: assets are addressed by absolute raw URLs

Nothing in this repo is linked relatively. PDFs are referenced as:

```
https://raw.githubusercontent.com/leissa/leissa/main/pdf/<slug>.pdf
```

Consequences:

- The URLs are pinned to `main`, so an asset only becomes reachable once pushed to `main`. A freshly added PDF or image 404s until then — including in the weekly link check.
- `images/` is an asset host for the **sibling PLaC group website** in `../` (`/home/roland/plac/website`, a separate Makefile-driven project whose `pages/` embed `https://raw.githubusercontent.com/leissa/leissa/main/images/*.jpg`). Renaming or deleting anything in `images/` breaks that site, and grepping this repo alone will not reveal the reference.
- That sibling project's `build.sh` also slices everything after `## 📖 Publications` out of *this* `README.md` and runs it through pandoc into its `publications.html`. So the generated section has to stay pandoc-safe: raw `<a id="…">` anchors (pandoc renumbers headings, so the year headings alone would not be linkable there) and `<details>` blocks with blank lines around their Markdown content.

## Conventions for publication entries

Sections are year-based and newest-first; within a year, `publications.yaml` order is preserved. Every year sits in a `<details open>` block, so a reader can fold a year away but sees all of them by default. Entries flagged `selected` are also listed compactly under `### ⭐ Selected`.

`id` — which is also the PDF slug — is author initials plus a two-digit year, in author order: `lg26` = Leißa, Griebler 2026; `lumh25` = Leißa, Ullrich, Meyer, Hack 2025; `bbhlmz13` = Braun, Buchwald, Hack, Leißa, Mallon, Zwinkau 2013.

Adding a paper is: drop `pdf/<id>.pdf` in place, add the entry to `publications.yaml`, run `tools/gen.py --bib`, commit all of it together.

The rules `tools/gen.py` encodes, which matter when changing it:

- Badge order: venue → PDF → publisher DOI → arXiv → Zenodo → award → GitHub artifact → YouTube → **dblp last**. `style=flat-square` throughout; grey badges for PDF/YouTube/dblp, blue for venues and DOIs.
- Two publisher-badge idioms, one per publisher: **named label** (`ACM-<doi>`, `Springer-<doi>`, `Elsevier-<doi>`, `World_Scientific-<doi>`, `%E2%8C%82_UdS-<doi>`) and **logo-only label** for IEEE and Zenodo, where the left segment is empty and carries just the logo — `badge/-<doi>-blue?style=flat-square&logo=ieee&labelColor=555&logoSize=auto`. The alt text names the publisher and must match the DOI prefix; the `PUBLISHERS` table maps prefix → (alt, label, parameters).
- A DOI is written **once** in `publications.yaml` and used for both the badge label and the href, so the two cannot drift apart — this used to be the most common defect here. shields escaping (`-` → `--`, `_` → `__`, space → `_`, literal `/`, never `%2F`) is `esc()`'s job.
- All DOI links go through `https://doi.org/<doi>` — not `dx.doi.org`, not `dl.acm.org/doi/abs/...`.
- `dblp` is the record key for *this* paper, i.e. what follows `/rec/` in the record URL. Do not hand-guess it from author initials; dblp's own numbering and homonym digits make that unreliable. <https://dblp.org/pid/05/10957.xml> lists the authoritative `key` and `ee` (DOI) for every publication — the fastest way to verify a batch. (It sits behind a bot challenge, so a browser may be needed.)
- Venue badges link to the venue for *that* year (conference site, or the publisher's volume/proceedings TOC). Copying a neighbouring entry tends to leave a link pointing at the wrong year's site.
- `leissa.bib` records come from doi.org content negotiation, but authors and titles are overridden from `publications.yaml`: publisher metadata mangles non-ASCII names (IEEE turns *Leißa* into *Leiba*/*Leisa*).
