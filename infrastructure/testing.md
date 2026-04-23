---
title: "Testing Strategy"
owners: []
soft_links: [/architecture/skill-system.md, /skills/qa/NODE.md]
---

# Testing Strategy

gstack tests skills at three tiers. The tiers exist because testing a
skill is fundamentally unlike testing a function — skills are
prose + bash blocks that run through a real model. The tests have to
reflect that, without letting CI cost explode.

---

## The three tiers

### Tier 1: Static validation (free)

Parses `SKILL.md` files and validates them against structural rules:

- Referenced `$B <command>` names exist in the browse CLI registry.
- Template placeholders resolve.
- Front-matter schema is valid.
- Skills declare only the tools they actually use.

Runs on every `bun test`. No model calls. Cheap enough to run on
every commit without thinking about it.

### Tier 2: E2E via `claude -p` (~$3.85 per full run)

Spawns a real Claude session as a subprocess (`claude -p`) and runs
each skill against a known-good harness. Streams NDJSON in real
time, parses the transcript, checks for errors.

**Not** run via the Agent SDK — skills invoke the `Skill` tool, and
the SDK cannot nest Agent invocations. Subprocess is the only way
to test a skill "in situ" with the host agent's actual routing.

**Diff-based selection:** each test declares file dependencies in
`test/helpers/touchfiles.ts`. Changes to the preamble, to
`gen-skill-docs`, or to the touching-file itself trigger all tests.
Otherwise only tests whose declared files changed run. `EVALS_ALL=1`
forces full run.

### Tier 3: LLM-as-judge (~$0.15 per judgment)

Uses Sonnet as a judge to score skill output on clarity,
completeness, and actionability. Gated behind `EVALS=1` — not
routine per-PR.

Judging is scalable where human review is the bottleneck. It is
imperfect (the judge has the same blind spots as the tested model)
but better than "does this look right" by eye.

---

## Why this split

- **Static validation** catches structural errors before any
  model call. Free, fast, runs always.
- **E2E via `claude -p`** is the only way to verify a skill works
  end-to-end — template substitution, preamble behavior, tool
  routing, `AskUserQuestion` flow. Expensive enough that we gate it
  (diff-based selection).
- **LLM-as-judge** is the quality dimension. A skill can pass E2E
  (it ran without error) and still produce sloppy output. Judge
  catches that.

No tier is redundant. Removing any one of them creates a class of
bug CI misses.

---

## CI workflows

- **`evals.yml`** — runs tier 2 on Ubicloud hosts, matrix-gated on
  PR diff. Weekly cron runs the full tier 2 + tier 3 matrix.
- **`skill-docs.yml`** — validates `SKILL.md` freshness by running
  `gen:skill-docs --dry-run` and failing if `git diff` is non-empty.
  Keeps the generated files from drifting from the template.

Both run on a pre-baked Docker image (`ci/`) with Playwright +
Chromium already installed so CI cold-start is minimized.

---

## E2E failure protocol

When an E2E test fails on a PR branch, the default claim
"it's pre-existing, not my change" is **not** acceptable without
evidence. The required protocol:

1. Run the same eval on `main` (or the base branch).
2. If it passes on base → the change broke it. Trace the blame.
3. If it fails on base too → cite the run and call it out as a
   pre-existing risk.

Unverified pre-existing claims are recorded as a tree-level
constraint because they were a recurring source of shipped bugs
before the protocol was enforced. Proof or flag — not "probably
fine".

---

## What this does not cover

- **Unit tests of `lib/` and `bin/` code.** Those are standard Bun
  tests under `test/`, not skill-level tests, and do not need a
  tree decision.
- **QA of user-facing products** built *with* gstack. That is what
  the **[/skills/qa/NODE.md](../skills/qa/NODE.md)** cluster is for,
  not this page.
- **Exact costs.** The numbers ($3.85, $0.15) are illustrative and
  drift with provider pricing. The ordering (tier 1 free, tier 2
  cheap-but-not-free, tier 3 gated) is the decision that persists.
