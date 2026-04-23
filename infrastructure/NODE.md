---
title: "Infrastructure"
owners: []
soft_links: [/architecture, /conventions]
---

# Infrastructure

Shared runtime and build-time systems that are not skills themselves
but are load-bearing for the skill library. This subtree is small on
purpose — the tree captures *decisions* about infrastructure, not
deployment instructions or schema dumps.

---

## Leaf nodes

- **[telemetry.md](telemetry.md)** — Opt-in Supabase telemetry. What
  it collects, what it does not, why edge functions gate writes.
- **[testing.md](testing.md)** — Three-tier test strategy (static,
  E2E via `claude -p`, LLM-as-judge), diff-based selection, cost
  gating.

---

## Not covered here (intentionally)

- **The browser daemon** is foundational to the *skills*, not just
  infrastructure. It lives in
  **[/architecture/browser-daemon.md](../architecture/browser-daemon.md)**.
- **Host adapters** (`hosts/` directory) are architecture-level;
  see **[/architecture/host-integration.md](../architecture/host-integration.md)**.
- **`bin/` compiled CLI utilities** are execution detail, not
  decisions. Read the source (`bin/gstack-config`,
  `bin/gstack-slug`, etc.) when you need to know what they do.
- **`supabase/migrations/` schema** belongs in the source repo;
  only the *decisions* about telemetry live in
  **[telemetry.md](telemetry.md)**.

See the parent-domain principle in
**[/.agents/skills/first-tree/references/principles.md](../.agents/skills/first-tree/references/principles.md)**:
"If an agent needs this information to *decide*, it belongs in the
tree. If only to *execute*, it stays in the source system."
