---
title: "Review"
owners: []
soft_links: [/conventions/second-opinion.md, /skills/safety/NODE.md]
---

# Review

Pre-landing review of a diff. These skills catch the classes of
problems that linters and test suites miss — structural issues, trust
boundary violations, security vulnerabilities, and "plausible-looking
but wrong" code that AI-generated diffs are prone to.

Review happens **before** `/ship` creates the PR (so problems are
caught early) and **again** through external review bots on GitHub
(CodeRabbit, Greptile, etc.) after the PR opens. The gstack review
skills own the first pass; the external bots are complementary.

---

## Member skills

- **`/review`** — Pre-landing PR review. Analyzes the diff against the
  base branch, looking for SQL safety issues, LLM trust boundary
  violations, conditional side effects, completeness gaps, and other
  structural problems. Can auto-fix obvious cases.
- **`/codex`** — Second opinion. Invokes OpenAI Codex CLI
  independently on the same diff, in three modes:
  - **Review** — pass/fail gate, independent verdict.
  - **Challenge** — adversarial mode that tries to break the code.
  - **Consult** — open-ended question with session continuity for
    follow-ups.
- **`/cso`** — Chief Security Officer mode. Infrastructure-first
  security audit: secrets archaeology, dependency supply chain,
  CI/CD pipeline security, LLM/AI security, skill supply-chain
  scanning, OWASP Top 10, STRIDE threat modeling, active
  verification. Two modes: **daily** (zero-noise, 8/10+ confidence
  gate) and **comprehensive** (monthly, 2/10 bar).

---

## Shared decisions

- **The second-opinion pattern is structural, not optional.** `/review`
  is Claude; `/codex` is OpenAI. When both have run on the same diff,
  agreement is a strong signal; divergence is a signal the user should
  read both verdicts. See
  **[/conventions/second-opinion.md](../../conventions/second-opinion.md)**.
- **Review can auto-fix, but must commit atomically.** A review that
  makes a silent multi-concern commit defeats the purpose. Each fix
  is its own commit with a clear message.
- **Security is tiered.** `/cso` daily mode is meant to run without
  noise — only 8/10+ confidence findings. Comprehensive mode is for
  the monthly sweep where we tolerate a higher false-positive rate in
  exchange for broader coverage.
- **Review does not merge.** The skills report and (where safe) fix.
  Merging is `/land-and-deploy`'s job. Separation of concerns prevents
  "the reviewer force-pushes because it thinks it knows better" (a
  violation of **[/product/ethos.md](../../product/ethos.md)**: User
  Sovereignty).

---

## When each runs

- `/ship`'s Review Readiness Dashboard invokes `/review` automatically
  on most diffs.
- `/codex` is opt-in per-diff (or run via `/autoplan` / `/benchmark-models`
  as part of a broader workflow).
- `/cso` is on a separate cadence — typically weekly or monthly, not
  per-PR. A full security audit is too heavy for every diff.

---

## Cross-references

- Pattern for cross-model verification:
  **[/conventions/second-opinion.md](../../conventions/second-opinion.md)**.
- Safety-skills cluster for runtime guards (not code review):
  **[/skills/safety/NODE.md](../safety/NODE.md)**.
- Release pipeline that invokes review:
  **[/skills/release/NODE.md](../release/NODE.md)**.
