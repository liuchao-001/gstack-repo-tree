---
title: "Docs"
owners: []
soft_links: [/skills/release/NODE.md, /product/design-system.md]
---

# Docs

Skills that keep documentation current and produce finished document
artifacts. The shared concern: docs drift without mechanical help. A
CHANGELOG written at ship time is better than a CHANGELOG written
"later"; a PDF generated from the source-of-truth markdown is better
than a separately-maintained parallel doc.

---

## Member skills

- **`/document-release`** — Post-ship documentation update. Reads all
  project docs, cross-references the diff, updates
  `README` / `ARCHITECTURE` / `CONTRIBUTING` / `CLAUDE.md` to match
  what shipped. Polishes CHANGELOG voice, cleans up TODOS, optionally
  bumps VERSION. Auto-invoked by `/ship` at step 13.
- **`/make-pdf`** — Turn any markdown file into a publication-quality
  PDF. Proper 1-inch margins, intelligent page breaks, page numbers,
  cover pages, running headers, curly quotes and em dashes, clickable
  TOC, diagonal DRAFT watermark when relevant. Not a draft artifact —
  a finished artifact.

---

## Shared decisions

- **Docs update is part of ship, not a follow-up.** `/document-release`
  is called by `/ship` automatically. This is **Boil the Lake** (see
  **[/product/ethos.md](../../product/ethos.md)**): a ship that leaves
  docs stale is not shipped.
- **Voice is enforced, not suggested.** CHANGELOG entries follow the
  rules in **[/product/design-system.md](../../product/design-system.md)**
  (no em-dashes in prose, no AI vocabulary, real numbers).
  `/document-release` rewrites entries that drift.
- **Markdown is the source.** `/make-pdf` reads markdown and renders
  PDF. There is no parallel "doc system" that users edit separately.
- **PDFs are finished artifacts.** A generated PDF with DRAFT
  watermark is better than a polished-looking PDF with wrong content.
  The watermark is a feature.

---

## Cross-references

- Release pipeline that invokes `/document-release`:
  **[/skills/release/NODE.md](../release/NODE.md)**.
- Voice rules the docs enforce:
  **[/product/design-system.md](../../product/design-system.md)**.
- The CHANGELOG conventions (per-branch, all commits since base):
  **[/conventions/versioning.md](../../conventions/versioning.md)**.
