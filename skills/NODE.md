---
title: "Skills"
owners: []
soft_links: [/product, /architecture/skill-system.md]
---

# Skills

The skill library, organized by **concern** — what an agent needs to
know to decide — not by directory layout. Every skill in the source
repo belongs to exactly one subdomain here. Cross-cutting patterns
(voice triggers, proactive suggestion, second opinion, learnings,
checkpointing) live in **[/conventions/](../conventions/NODE.md)** so
they are not repeated twelve times.

If you are adding a new skill, pick the subdomain whose `NODE.md`
describes the shared problem your skill is solving. If your skill does
not fit anywhere, that is a signal to either scope it down or propose
a new subdomain in review — not to silently stuff it into the
closest-looking folder.

---

## The workflow frame

Most gstack skills fit a Think → Plan → Build → Review → Test → Ship →
Reflect pipeline. The subdomains below roughly follow that order, with
safety and browser infrastructure as cross-cutting columns.

| Stage | Primary subdomain | Supporting |
|-------|------------------|------------|
| Think | [planning/](planning/NODE.md) | [debug/](debug/NODE.md) (`/investigate`) |
| Plan | [planning/](planning/NODE.md), [design/](design/NODE.md) | [devex/](devex/NODE.md) |
| Build | (user code; few skills own "build") | [safety/](safety/NODE.md) |
| Review | [review/](review/NODE.md) | [design/](design/NODE.md) (visual review), [devex/](devex/NODE.md) |
| Test | [qa/](qa/NODE.md) | [browser/](browser/NODE.md) |
| Ship | [release/](release/NODE.md) | [docs/](docs/NODE.md) |
| Reflect | [observability/](observability/NODE.md) | [debug/](debug/NODE.md) (`/learn`) |

---

## Subdomains

- **[planning/](planning/NODE.md)** — `/office-hours`, `/plan-ceo-review`,
  `/plan-eng-review`, `/plan-design-review`, `/plan-devex-review`,
  `/autoplan`. Rethinking scope, locking architecture, sequencing work.
- **[design/](design/NODE.md)** — `/design-consultation`,
  `/design-shotgun`, `/design-html`, `/design-review`,
  `/plan-design-review`. From "no design system" through "live site
  polish".
- **[qa/](qa/NODE.md)** — `/qa`, `/qa-only`. Systematic test-then-fix
  with real browser evidence.
- **[release/](release/NODE.md)** — `/ship`, `/land-and-deploy`,
  `/setup-deploy`, `/canary`. The path from "code ready" to "prod
  verified".
- **[review/](review/NODE.md)** — `/review`, `/codex`, `/cso`.
  Pre-landing PR review, cross-model second opinion, security audit.
- **[devex/](devex/NODE.md)** — `/plan-devex-review`, `/devex-review`.
  DX as a first-class product concern.
- **[observability/](observability/NODE.md)** — `/health`, `/retro`,
  `/benchmark`, `/benchmark-models`. Project health, weekly
  retrospectives, cross-model benchmarks.
- **[safety/](safety/NODE.md)** — `/careful`, `/freeze`, `/guard`,
  `/unfreeze`. Guardrails for destructive and out-of-scope operations.
- **[browser/](browser/NODE.md)** — `/browse`, `/open-gstack-browser`,
  `/pair-agent`, `/setup-browser-cookies`, `/connect-chrome`. The
  skills that sit on top of the browser daemon.
- **[debug/](debug/NODE.md)** — `/investigate`, `/learn`,
  `/context-save`, `/context-restore`. Root-cause debugging, memory
  management, session persistence.
- **[docs/](docs/NODE.md)** — `/document-release`, `/make-pdf`.
  Keeping docs in sync with what shipped; converting markdown to
  finished PDFs.
- **[meta/](meta/NODE.md)** — `/plan-tune`, `/gstack-upgrade`. Skills
  about gstack itself.

---

## Cross-cutting conventions (do not duplicate in subdomain nodes)

- **Voice triggers** — every skill declares speech-to-text aliases.
  See **[/conventions/voice-triggers.md](../conventions/voice-triggers.md)**.
- **Proactive vs. advisory** — user-level preference, default proactive.
  See **[/conventions/proactive-suggestion.md](../conventions/proactive-suggestion.md)**.
- **Second opinion** — many skills pair with `/codex`. See
  **[/conventions/second-opinion.md](../conventions/second-opinion.md)**.
- **Learnings** — every skill ends by logging. See
  **[/conventions/learnings.md](../conventions/learnings.md)**.
- **Checkpointing** — `/ship` filter-squashes WIP commits. See
  **[/conventions/checkpointing.md](../conventions/checkpointing.md)**.
