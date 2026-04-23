---
title: "Versioning and CHANGELOG"
owners: []
soft_links: [/skills/release/NODE.md, /skills/docs/NODE.md]
---

# Versioning and CHANGELOG

gstack versions and CHANGELOG entries are **branch-scoped**, not
commit-scoped and not PR-title-scoped. This is a structural decision
that shapes how `/ship` works and what a CHANGELOG entry looks like.

Canonical source: `VERSION` file and `CHANGELOG.md` in the gstack
source repo.

---

## Version format: `X.Y.Z.W`

`major.minor.patch.revision`

- **Major** — thesis change ("single-user" → "multi-user" would bump
  this).
- **Minor** — user-visible feature set additions.
- **Patch** — bug fixes, non-breaking improvements within a feature.
- **Revision** — ship-level increments within a branch (multiple
  ships per week without starting a new patch cycle).

The fourth component is unusual and load-bearing. It lets every
feature branch bump its own version and get its own CHANGELOG entry
without colliding with concurrent work on other branches.

**Why:** with aggressive branch-per-feature shipping, two branches
can both be "the next patch". A three-component scheme forces them
to serialize merges or cherry-pick; a four-component scheme lets them
each take a revision slot.

---

## CHANGELOG format

Each branch gets **one entry**, written at `/ship` time, covering
**all commits in the branch** relative to the base branch. Not
per-commit, not per-PR title.

Structure:

1. **Two-line bold headline** — the landing message.
2. **Lead paragraph** — what changed in one short paragraph.
3. **Metrics table** — real numbers (commits, files, LOC, perf
   delta) where applicable.
4. **"What this means" closer** — the user-impact framing.

Voice rules (from
**[/product/design-system.md](../product/design-system.md)**):

- No em-dashes.
- No AI vocabulary ("leveraging", "robust", "seamlessly",
  "comprehensive", "best-in-class").
- Real numbers over superlatives.
- Short paragraphs.
- Directness over politeness.

`/document-release` rewrites entries that drift from these rules.

---

## Why branch-scoped instead of per-PR-title

PR titles are written once at open time and often drift from what
actually shipped (scope expanded, multiple PRs merged into one, rebase
reshuffled commits). A CHANGELOG entry written at `/ship` time, after
all commits exist, reflects what actually went out.

It also means a single PR with twelve commits gets one coherent
CHANGELOG entry, not twelve fragments the reader has to synthesize.

---

## Where this is enforced

- **`/ship`** — reads the branch's commits, drafts the entry, bumps
  VERSION.
- **`/document-release`** — polishes voice, normalizes structure,
  catches drift.
- **CI check** (`skill-docs.yml` pipeline territory) — can fail a PR
  whose CHANGELOG entry is missing or empty.

---

## What this does not cover

- **Semantic versioning promises.** The scheme is about predictable
  concurrent shipping, not backwards-compatibility semantics. We do
  not promise that a minor bump is non-breaking the way strict semver
  does.
- **Per-skill versioning.** All skills share one `VERSION`. There is
  no "`/qa` v2 while `/ship` stays v1". See
  **[/architecture/skill-system.md](../architecture/skill-system.md)**.
