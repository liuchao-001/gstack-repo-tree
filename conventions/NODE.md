---
title: "Conventions"
owners: []
soft_links: [/product, /architecture/skill-system.md, /skills/NODE.md]
---

# Conventions

Cross-cutting patterns that apply across multiple skills. Documented
here once, referenced from the subdomain nodes that use them. This is
the "how we do things at gstack" layer that every contributor needs to
internalize.

If you find yourself re-stating one of these patterns in a
subdomain's `NODE.md`, move it here instead and link. If a pattern
applies to only one subdomain, it belongs in that subdomain, not here.

---

## Leaf nodes

- **[versioning.md](versioning.md)** — `X.Y.Z.W` format, branch-scoped
  bumps, CHANGELOG format (per-branch, covers all commits since base).
- **[proactive-suggestion.md](proactive-suggestion.md)** — `PROACTIVE`
  config pattern; when the agent invokes vs. suggests.
- **[second-opinion.md](second-opinion.md)** — Cross-model review via
  `/codex`; how overlap signals trust and divergence signals
  uncertainty.
- **[voice-triggers.md](voice-triggers.md)** — Natural-language
  aliases in skill front-matter; why they exist in V1 despite not
  being the primary routing path.
- **[learnings.md](learnings.md)** — How `learnings.jsonl` is
  written, loaded, and pruned; the compound-learning pattern.
- **[checkpointing.md](checkpointing.md)** — Continuous WIP commits
  during long sessions; filter-squash on `/ship` so PRs stay clean.
- **[community-guardrails.md](community-guardrails.md)** — ETHOS,
  voice, and promotional material are not open to external
  rewrites; `AskUserQuestion` before accepting those changes.
