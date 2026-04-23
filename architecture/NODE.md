---
title: "Architecture"
owners: []
soft_links: [/product, /skills, /infrastructure]
---

# Architecture

The load-bearing abstractions that every skill depends on. If you are
adding a new skill, read this subtree first — these decisions determine
what you can assume is already solved and what you have to do yourself.

gstack is not a monolith; it is a set of slash-command skills on top of
a small shared runtime. The pieces documented here are the runtime:
what a "skill" actually is, how the persistent browser daemon works,
how the security model protects the daemon when exposed to a remote
agent, and how the multi-host adapter layer lets the same skills run
under Claude Code, Codex, OpenClaw, and others.

Implementation code lives in the source repo (`browse/`, `extension/`,
`openclaw/`, `hosts/`, `lib/`, `bin/`, `scripts/`). This subtree
captures the *why* — constraints these abstractions exist to satisfy
and the tradeoffs we have already locked in.

---

## Leaf nodes

- **[skill-system.md](skill-system.md)** — What a skill is, the
  `SKILL.md.tmpl` + `gen:skill-docs` pipeline, template placeholders,
  and the preamble contract.
- **[browser-daemon.md](browser-daemon.md)** — Why we run a persistent
  Chromium daemon instead of cold-starting Playwright per command;
  state file, port scheme, idle timeout, health check.
- **[security-model.md](security-model.md)** — Dual-listener design
  (local full-surface + tunnel allowlist), scoped tokens, why we rely
  on physical port separation instead of header inference.
- **[host-integration.md](host-integration.md)** — How the same
  skills run under multiple host agents via the `hosts/` adapter
  layer; why Claude is the reference host.

---

## Cross-references

- The testing strategy that keeps these abstractions honest is in
  **[/infrastructure/testing.md](../infrastructure/testing.md)**.
- The telemetry that watches them in production is in
  **[/infrastructure/telemetry.md](../infrastructure/telemetry.md)**.
- Which skills use which abstraction is mapped under
  **[/skills/](../skills/NODE.md)**.
