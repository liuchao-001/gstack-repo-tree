---
title: "Developer Experience"
owners: []
soft_links: [/skills/planning/NODE.md, /skills/design/NODE.md, /architecture/browser-daemon.md]
---

# Developer Experience

DX as a first-class product concern, not a "wait until someone
complains" concern. These skills apply the same plan-stage + live-audit
split that **[design/](../design/NODE.md)** uses, but for developer-
facing surfaces (APIs, CLIs, SDKs, libraries, platforms, docs).

---

## Member skills

- **`/plan-devex-review`** — Plan-stage review (also listed under
  **[/skills/planning/](../planning/NODE.md)**). Interactive:
  explores developer personas, benchmarks against competitors, designs
  "magical moments", traces friction points. Three modes:
  - **DX EXPANSION** — competitive advantage.
  - **DX POLISH** — bulletproof every touchpoint.
  - **DX TRIAGE** — critical gaps only.
- **`/devex-review`** — Live audit. Uses the browser daemon to
  actually *try* the DX: navigates the docs, follows the getting-
  started flow, times TTHW (Time To Hello World), screenshots error
  messages, evaluates CLI help text. Produces a DX scorecard with
  evidence.

---

## The "boomerang" pattern

`/plan-devex-review` scores the plan. `/devex-review` runs after ship
and scores reality. The two get compared:

- Plan said TTHW was 3 minutes; reality shows 8 minutes → what broke
  the assumption?
- Plan scored onboarding 9/10; reality scored 6/10 → which gap
  appeared between plan and ship?

The comparison is the feedback loop that makes plan-stage scoring
trustworthy over time.

---

## Shared decisions

- **DX is a plannable artifact.** Personas, magical moments, TTHW
  benchmarks are written down before code — not discovered after.
- **"Try it" beats "read it".** `/devex-review` uses the real browser
  and real CLIs because hand-waving about DX is how bad DX happens.
- **Friction is measurable.** Error message quality, help text
  clarity, onboarding step count are scored, not hand-waved.

---

## When each runs

- `/plan-devex-review` during planning, **only if the product has a
  developer-facing surface**. Skip for pure end-user UI work.
- `/devex-review` after ship, same scoping. Proactively suggested when
  the user has just shipped something DX-adjacent.

---

## Cross-references

- Plan-stage reviews (CEO / Eng / Design / DevEx) together form the
  planning gauntlet: **[/skills/planning/NODE.md](../planning/NODE.md)**.
- Live audit shares tooling with **[/skills/design/NODE.md](../design/NODE.md)**
  (`/design-review`) and runs on the **[browser daemon](../../architecture/browser-daemon.md)**.
- DX of *gstack itself* is tracked separately (meta-skill work lives
  under **[/skills/meta/NODE.md](../meta/NODE.md)**).
