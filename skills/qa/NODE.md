---
title: "QA"
owners: []
soft_links: [/skills/browser/NODE.md, /skills/release/NODE.md, /architecture/browser-daemon.md]
---

# QA

The "give the agent eyes" cluster. These skills drive a real browser
against the real product, find real bugs, and either fix them or
report them. The QA unlock was the point where parallelism went from
6 to 12 workers — an agent that can actually test what it built is
dramatically more useful than one that assumes.

---

## Member skills

- **`/qa`** — Test + fix. Opens a real browser, systematically
  exercises the feature, finds bugs, fixes them in source with
  atomic commits, re-verifies. Three tiers: **Quick** (critical +
  high), **Standard** (+ medium), **Exhaustive** (+ cosmetic).
  Produces before/after health scores and ship-readiness summary.
- **`/qa-only`** — Report mode. Same methodology, produces a
  structured bug report with screenshots and repro steps, but never
  edits code. Use when the QA output feeds a human triager or a
  separate fix session.

The **`/browse`** CLI tool is listed under
**[/skills/browser/](../browser/NODE.md)**, not here — it is the
runtime both QA skills use, but it is not a QA workflow on its own.

---

## Method

Both skills share the same methodology block (`{{QA_METHODOLOGY}}`
template placeholder — see
**[/architecture/skill-system.md](../../architecture/skill-system.md)**):

1. Enumerate the golden path and every edge case derived from the plan.
2. Drive the browser through each case. Capture evidence (screenshots,
   console logs, network requests).
3. Classify findings by severity: **critical** (blocks ship),
   **high** (user-visible regression), **medium** (functional but
   wrong), **cosmetic** (polish).
4. For `/qa`: fix critical + high, re-verify, commit atomically; loop.
5. For `/qa-only`: write report with repro steps and stop.

The tiered approach lets the user scope effort: Quick for a
before-lunch check, Exhaustive before a major release.

---

## Shared decisions

- **Real browser, real state.** No mocks, no JSDOM, no Storybook. If
  the page fails in a real browser, that is what we want to find.
- **Atomic commits during fix loop.** Each bug fix is its own commit
  so bisect stays clean and the QA session's diff is reviewable.
- **Evidence is required for findings.** A bug report without a
  screenshot or reliable repro is not a bug report.
- **Severity gates ship, not count.** One critical bug blocks;
  forty cosmetic issues do not.

---

## Cross-references

- Runtime: **[/architecture/browser-daemon.md](../../architecture/browser-daemon.md)**
  and the `/browse` tool under **[/skills/browser/](../browser/NODE.md)**.
- Post-deploy continuation: **[/skills/release/NODE.md](../release/NODE.md)**
  (`/canary` watches prod after `/ship`).
- Visual side of QA is the **[design/](../design/NODE.md)** domain's
  `/design-review`. Functional vs. visual QA split is by concern, not
  by tool.
