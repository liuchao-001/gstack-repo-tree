---
title: "Second Opinion (Cross-Model Review)"
owners: []
soft_links: [/skills/review/NODE.md, /skills/observability/NODE.md]
---

# Second Opinion (Cross-Model Review)

gstack is built on Claude, but it deliberately *does not* assume
Claude is always right. Several skills pair with `/codex` (OpenAI
Codex CLI) to get an independent second opinion on the same diff.
This document explains the pattern and how to read its signals.

---

## The pattern

1. A primary review skill (typically `/review`, sometimes `/cso`) runs
   on the diff with Claude.
2. `/codex` runs on the same diff with GPT (via the Codex CLI).
3. The user (or a meta-skill like `/autoplan`) compares the two
   verdicts.

Three possible outcomes:

- **Both find the same issues** → high confidence those issues are
  real. Fix them.
- **Only one finds an issue** → lower confidence. Read the finding;
  decide whether model-specific bias is in play.
- **Neither finds anything** → higher confidence the diff is clean.
  Not proof, but a stronger signal than either alone.

---

## Why this is structural, not optional

Any single model has systematic blind spots. A review that trusts
Claude's "looks good to me" is fine right up until Claude's blind
spot intersects your production bug. Cross-model verification is not
a nice-to-have; it is how you catch the miss.

This is **Search Before Building** (see
**[/product/ethos.md](../product/ethos.md)**) applied to review: do
not ship based on one opinion when another is cheap to get.

---

## Where it shows up

- **`/codex`** is the dedicated skill (see
  **[/skills/review/NODE.md](../skills/review/NODE.md)**). Three
  modes: review (pass/fail), challenge (adversarial), consult (open
  question with session continuity).
- **`/autoplan`** runs the reviewer gauntlet and includes `/codex`
  as one of the reviewers when the diff warrants it.
- **`/benchmark-models`** (see
  **[/skills/observability/NODE.md](../skills/observability/NODE.md)**)
  is the meta-application: run the same skill prompt through
  multiple models and compare latency, tokens, cost, and quality.
  The same "multiple independent opinions" plumbing.

---

## When to skip it

- **Trivial diffs.** A typo fix does not need cross-model review.
- **Time-sensitive fixes** where the cost of waiting for a second
  opinion exceeds the cost of rolling back a bad deploy.
- **Proprietary or sensitive code** where sending the diff to another
  provider is not acceptable. `/codex` uses the OpenAI API; if that
  is a dealbreaker, skip it.

---

## What this does not guarantee

- **Correctness.** Two models agreeing on "looks good" is a signal,
  not proof. Humans still own the merge decision.
- **Coverage of the same classes of bugs.** Claude and GPT overlap
  heavily on common patterns. They can both miss the same obscure
  issue. More models would help; we do not ship `/gemini` yet.
- **Consistency across runs.** Same diff, different day, different
  output. Expect some variance.
