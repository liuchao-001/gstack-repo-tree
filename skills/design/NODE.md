---
title: "Design"
owners: []
soft_links: [/product/design-system.md, /skills/planning/NODE.md, /skills/qa/NODE.md]
---

# Design

Skills that turn visual design from "I'll figure it out later" into a
first-class step with the same rigor as code review and QA. The shared
concern: make design decisions visible, comparable, and fixable — at
plan stage, at implementation stage, and after ship.

The aesthetic rules these skills enforce are in
**[/product/design-system.md](../../product/design-system.md)** (CLI
heritage, industrial, amber accent, specific fonts).

---

## Member skills

- **`/design-consultation`** — From scratch. Researches the landscape,
  proposes a full design system (aesthetic, type, color, layout,
  spacing, motion), generates preview pages. Writes `DESIGN.md` as the
  project's design source of truth.
- **`/design-shotgun`** — Exploration. Generates 4–6 AI mockup variants
  (via image generation), opens a comparison board, collects
  structured feedback, iterates. Includes taste-learning that decays
  at 5%/week.
- **`/design-html`** — Implementation finalization. Turns an approved
  mockup into production-quality Pretext-native HTML/CSS. Text
  actually reflows, heights compute, layouts adapt. 30KB overhead, zero
  dependencies, smart API routing per design type.
- **`/plan-design-review`** — Plan-stage review (also listed under
  **[/skills/planning/](../planning/NODE.md)**). Rates plan's design
  dimensions, fixes the plan. Report-first, no site changes.
- **`/design-review`** — Live-site review. Finds visual inconsistency,
  spacing issues, hierarchy problems, "AI slop" patterns. Iteratively
  fixes issues in source, commits each atomically, re-verifies with
  before/after screenshots.

---

## Pipeline

```
no design system yet
  → /design-consultation        [writes DESIGN.md]
  → /design-shotgun             [explores variants]
  → /design-html                [implements]
  → /design-review              [polishes live site]
```

Plan-stage review sits orthogonal: `/plan-design-review` gates before
`/design-html`, `/design-review` gates after.

---

## Shared decisions

- **Design is not optional.** gstack treats design with the same rigor
  as code — it is not a "later" concern.
- **Visual comparisons beat descriptions.** `/design-shotgun` exists
  because "describe a button style" is worse than "look at six buttons
  side by side".
- **Fix in source, not in comments.** `/design-review` edits code and
  re-screenshots. Logging a problem is never the end state.
- **Dark + amber is the default palette.** See
  **[/product/design-system.md](../../product/design-system.md)**.
  Light mode is opt-in.

---

## Cross-references

- Voice and prose rules (applies to all generated text, not just
  design): **[/product/design-system.md](../../product/design-system.md)**.
- Visual QA overlaps with functional QA:
  **[/skills/qa/NODE.md](../qa/NODE.md)**.
- The browser daemon is the runtime for screenshots and before/after
  diffs: **[/architecture/browser-daemon.md](../../architecture/browser-daemon.md)**.
