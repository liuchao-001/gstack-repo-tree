---
title: "Planning"
owners: []
soft_links: [/skills/design/NODE.md, /skills/review/NODE.md, /skills/devex/NODE.md]
---

# Planning

Skills that think *before* code. The shared concern: turn a vague
user request ("I want to add X") into a scoped, defensible plan that
downstream build / review / QA / ship skills can execute against.

Each planning skill puts on a different role's hat (CEO, Eng Manager,
Designer, DX Lead) and rereads the plan from that perspective. The
output is a structured design doc, not code.

---

## Member skills

- **`/office-hours`** — YC-style diagnostic. Six forcing questions that
  expose demand reality, status quo, desperate specificity, narrowest
  wedge, observation, and future-fit. Runs **before** there is a plan
  at all; output is a design doc that seeds the rest.
- **`/plan-ceo-review`** — Founder/CEO mode. Four explicit modes:
  SCOPE EXPANSION (find the 10-star product), SELECTIVE EXPANSION
  (hold scope, cherry-pick expansions), HOLD SCOPE (maximum rigor on
  what is already there), SCOPE REDUCTION (strip to essentials).
  Challenges premises, not implementation.
- **`/plan-eng-review`** — Eng Manager mode. Locks in architecture,
  data flow, ASCII diagrams, state machines, test matrix, failure
  modes. This is the "is this actually buildable as specified" gate.
- **`/plan-design-review`** — Designer's eye at *plan* stage (not live
  site). Rates each design dimension 0–10, explains what a 10 looks
  like, fixes the plan to reach it. Interactive, per-decision
  `AskUserQuestion`.
- **`/plan-devex-review`** — DX Lead mode. Three modes: DX EXPANSION
  (competitive advantage), DX POLISH (bulletproof every touchpoint),
  DX TRIAGE (critical gaps only). Asks about personas, benchmarks,
  magical moments, friction points.
- **`/autoplan`** — Meta-skill. Runs all the reviews above sequentially
  with auto-decisions based on six decision principles, surfaces taste
  calls at a final approval gate. "Run the whole gauntlet without
  answering 15–30 questions."

---

## Sequencing

The typical plan pipeline:

```
/office-hours    → design doc
    ↓
/plan-ceo-review → scope decisions
    ↓
/plan-eng-review → architecture + test plan
    ↓
[/plan-design-review if UI]
[/plan-devex-review if developer-facing]
    ↓
implementation
```

`/autoplan` runs this pipeline automatically. Individual skills are
also usable standalone when only one review matters.

---

## Shared decisions

- **No plan skill writes code.** Output is prose, diagrams, decision
  tables. Implementation happens after.
- **Interactive by default, automatable on request.** Each skill uses
  `AskUserQuestion` per decision point; `/autoplan` replaces that with
  a decision principle set.
- **Ratings, not vibes.** 0–10 scores with "what would make it a 10"
  explanations. Taste stays explicit, not hidden.

---

## Cross-references

- Design reviews split across planning and execution:
  **[/skills/design/plan-design-review.md](../design/NODE.md)** vs
  `/design-review` (live).
- DX reviews split the same way — plan stage lives here; live audit is
  **[/skills/devex/NODE.md](../devex/NODE.md)**.
- Engineering review's test matrix feeds **[/skills/qa/NODE.md](../qa/NODE.md)**.
