---
title: "gstack"
owners: [liuchao-001]
---

# gstack

Context Tree for [garrytan/gstack](https://github.com/garrytan/gstack) — a suite
of opinionated Claude Code skills that turn a single builder into a virtual
engineering team (CEO, Eng Manager, Designer, Reviewer, QA, Security, Release,
and more). This tree captures the decisions, domain boundaries, and
cross-cutting constraints behind the skill collection, while the source repo
continues to hold the skill implementations, CLI, and docs.

---

<!-- BEGIN FIRST-TREE-SOURCE-REPO-INDEX -->
## Source Repos

- **[Source Repos](source-repos.md)** — Generated index of bound source/workspace repos and their GitHub URLs.
<!-- END FIRST-TREE-SOURCE-REPO-INDEX -->

## Domains

<!-- Baseline scaffold only. Top-level domains can be added below as they are
     populated. The current bound source repo (gstack) organizes naturally by
     skill category, CLI tooling, and documentation — grow the tree by concern
     (what an agent needs to know to decide), not by directory layout. -->

- **[members/](members/NODE.md)** — Team member definitions and responsibilities.

---

## Working with the Tree

Keep decision context here. Keep implementation detail in the source repos this
tree describes.

See [AGENTS.md](AGENTS.md) for agent instructions — the before/during/after workflow, ownership model, and tree maintenance.

See [whitepaper.md](.agents/skills/first-tree/references/whitepaper.md) for the First Tree white paper — Agent Team methodology, principles, and vision.

See the installed framework documentation:
- [principles.md](.agents/skills/first-tree/references/principles.md) — core principles with examples
- [ownership-and-naming.md](.agents/skills/first-tree/references/ownership-and-naming.md) — node naming and ownership model
