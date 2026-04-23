---
title: "Release"
owners: []
soft_links: [/skills/qa/NODE.md, /skills/docs/NODE.md, /conventions/versioning.md]
---

# Release

The path from "code ready" to "production verified". These skills own
the last-mile mechanics (tests, version bump, CHANGELOG, PR, merge,
deploy, canary) so the user does not have to remember the exact
sequence each time.

---

## Member skills

- **`/ship`** — The packaging step. Detects + merges base branch,
  runs tests, reviews the diff, bumps `VERSION`, writes CHANGELOG,
  commits, pushes, creates PR. Auto-invokes `/document-release` at the
  end so docs never lag. Includes a Review Readiness Dashboard that
  decides which review skills are appropriate for this diff.
- **`/land-and-deploy`** — The landing step. Merges the PR, waits for
  CI, triggers deploy, verifies production health via canary checks.
  Takes over where `/ship` leaves off.
- **`/setup-deploy`** — One-time configuration. Detects deploy platform
  (Fly, Render, Vercel, Netlify, Heroku, GitHub Actions, custom),
  production URL, health check endpoints, deploy commands. Writes
  configuration to `CLAUDE.md` so every future deploy is automatic.
- **`/canary`** — Post-deploy monitoring. Watches the live app for
  console errors, performance regressions, page failures. Takes
  periodic screenshots, compares against pre-deploy baselines, alerts
  on anomalies.

---

## Sequencing

```
code ready
  → /ship              [package: test, review, version, CHANGELOG, PR]
  → /land-and-deploy   [merge, CI, deploy, prod health]
  → /canary            [ongoing post-deploy watch]
```

`/setup-deploy` is one-time per project. Once `CLAUDE.md` has the
config, the rest runs without asking.

`/ship` at step 13 auto-invokes `/document-release` — see
**[/skills/docs/NODE.md](../docs/NODE.md)**.

---

## Shared decisions

- **One ship command, many internal steps.** The user types `/ship`
  once; the skill handles 13+ concerns (test bootstrap, coverage
  audit, CI detection, Review Readiness Dashboard, version bump,
  CHANGELOG, PR body, etc.). This is "Boil the Lake" (see
  **[/product/ethos.md](../../product/ethos.md)**) at the skill level.
- **CHANGELOG is per-branch, covering all commits since base.** Not
  per-commit, not per-PR title. See
  **[/conventions/versioning.md](../../conventions/versioning.md)**.
- **Smart routing for reviews.** The Review Readiness Dashboard inside
  `/ship` decides which reviews to run. CEO review does not run on
  infra bug fixes; design review does not run on a backend refactor.
- **Deploy is not optional once configured.** Once `/setup-deploy` has
  run, `/land-and-deploy` is always the next step after PR merge, not
  a separate manual decision.
- **Verification is part of release.** A deploy with no canary is not
  shipped; it is "probably shipped". `/canary` is the promotion gate.

---

## Cross-references

- Tests that `/ship` runs live under **[/skills/qa/NODE.md](../qa/NODE.md)**
  (interactive) and **[/infrastructure/testing.md](../../infrastructure/testing.md)**
  (CI tiers).
- Docs auto-update: **[/skills/docs/NODE.md](../docs/NODE.md)**.
- Version and CHANGELOG conventions:
  **[/conventions/versioning.md](../../conventions/versioning.md)**.
- Post-deploy health goes back into **[/skills/observability/NODE.md](../observability/NODE.md)**.
