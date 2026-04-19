# Copilot instructions for this repository

## Repository shape

This repository is a flat publication archive, not an application or library. The two important content surfaces are:

- `README.md`: the canonical, human-readable index of publications.
- Root-level `*.pdf` files: the assets linked from the README.

There is no source tree, package manifest, build system, lint configuration, or automated test suite in the current repository.

## Build, test, and lint commands

No build, test, or lint commands exist in this repository today.

## High-level architecture

The public-facing "site" is effectively the single `README.md` file rendered by GitHub. Publications are organized in reverse chronological order under `## 📖 Publications`, with one section per year.

Each publication entry combines metadata and asset links:

- a bold paper title
- an author line
- a sequence of badges and links for venue, PDF, DOI/publisher pages, and optional references such as arXiv, Zenodo, YouTube, dblp, or GitHub

The PDF badge URLs in `README.md` point to raw GitHub content on the `main` branch, for example:

`https://raw.githubusercontent.com/leissa/leissa/main/<filename>.pdf`

That means content changes are usually coupled across two places: the root PDF file list and the corresponding `README.md` entry. If one changes without the other, links break or publications disappear from the index.

## Key conventions

- Keep publication sections year-based and newest-first, matching the existing reverse-chronological structure in `README.md`.
- Keep PDFs at the repository root. Existing filenames are short slugs, usually derived from author initials plus year (for example `lumh25.pdf`, `bbhlmz13.pdf`).
- When adding or renaming a PDF, update the matching raw GitHub URL in `README.md`; do not switch to a different linking style unless the whole file is being normalized consistently.
- Match the existing publication entry formatting: bullet item, bold title, `<br>` line breaks for metadata, then indented badge links on following lines.
- Preserve the badge-heavy style already used in `README.md`; add only the links that are actually available for a publication, in the same general order used by nearby entries.
- Treat `README.md` as the primary curated artifact. Repository changes are usually content edits, not code refactors.
