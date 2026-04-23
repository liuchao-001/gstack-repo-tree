---
title: "Observability"
owners: []
soft_links: [/skills/release/NODE.md, /infrastructure/telemetry.md]
---

# Observability

Skills that tell you whether the codebase and the workflow are
healthy, not just whether the last build passed. The concern: trends
over time, not single-point states.

---

## Member skills

- **`/health`** — Code-quality dashboard. Wraps the project's existing
  tools (type checker, linter, test runner, dead-code detector, shell
  linter), computes a weighted composite 0–10 score, tracks trends
  across runs. Works as a ship gate when the score dips.
- **`/retro`** — Weekly engineering retrospective. Analyzes commit
  history, work patterns, code-quality metrics. Team-aware: per-person
  breakdown with praise and growth areas. Keeps persistent history so
  "how were we doing 3 weeks ago" is answerable.
- **`/benchmark`** — Performance regression detection for web pages.
  Uses the browser daemon to measure page-load time, Core Web Vitals,
  resource sizes. Establishes baselines, compares before/after on every
  PR. Tracks trends.
- **`/benchmark-models`** — Cross-model AI benchmark for gstack skills
  themselves. Runs the same prompt through Claude, GPT (via Codex CLI),
  and Gemini — compares latency, tokens, cost, and optionally quality
  via LLM judge. Answers "which model is actually best for this
  skill?" with data.

---

## Shared decisions

- **Composite scores over single metrics.** A project is healthier
  than its type errors alone. `/health` weights multiple signals into
  one number the user can trend.
- **History is the point.** `/retro` and `/health` both keep persistent
  history so week-over-week and release-over-release comparisons are
  cheap.
- **`/benchmark` is page performance; `/benchmark-models` is model
  performance.** They sound similar and do different things — the
  naming reflects that `/benchmark` is older and more canonical.
- **Quality judged by model, not by human review.** `/benchmark-models`
  uses LLM-as-judge where a human rater would be the bottleneck.
  Imperfect but scalable; better than vibes.

---

## Cross-references

- Release pipeline uses `/health` as a gate and `/canary` for
  post-deploy monitoring: **[/skills/release/NODE.md](../release/NODE.md)**.
- Runtime telemetry that feeds into health / retro dashboards:
  **[/infrastructure/telemetry.md](../../infrastructure/telemetry.md)**.
- Weekly retrospective is the closing loop of the Think → ... →
  Reflect workflow (see **[/skills/NODE.md](../NODE.md)**).
