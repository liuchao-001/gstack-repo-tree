---
title: "Host Integration"
owners: []
soft_links: [/product/v1-scope.md, /architecture/skill-system.md]
---

# Host Integration

gstack skills target Claude Code first, but the same skill files are
meant to run under other host agents too (Codex CLI, OpenClaw, Cursor,
Kiro, Factory, Hermes, OpenCode, Slate, GBrain). The `hosts/` directory
in the source repo holds per-host adapter configs.

This node captures the intent — *why* we support multiple hosts and
what it implies for skill authors — not the per-host config mechanics
(those are in `hosts/<name>.ts` in the source).

---

## Why multi-host

1. **Skill authors want reach.** A skill that works everywhere is more
   valuable than one locked to a single host.
2. **Users are not all on Claude Code.** OpenClaw users, Codex CLI
   users, Cursor users all benefit from the same workflow library.
3. **Model diversity is a product feature.** `/codex` and
   `/benchmark-models` explicitly rely on being able to invoke
   non-Claude agents as peers. If the host layer only knew Claude,
   those skills could not exist.

**The commitment:** a skill should work in any host whose capabilities
meet the skill's requirements. If the skill needs tools the host does
not expose, it fails gracefully with a clear "this host does not
support X" message — it does not silently degrade.

---

## Claude is the reference host

Not every host is equal. Claude Code is the host we develop and test
against. When behavior diverges between hosts:

- **Claude Code is the spec.** What gstack does there is "correct".
- **Other hosts are best-effort.** We fix gaps when we can, but the
  canonical behavior is what Claude Code does.
- **A skill that works only in a non-Claude host is a bug.** The point
  of multi-host is universal reach, not forks.

See **[/product/v1-scope.md](../product/v1-scope.md)** for the user
promise: "Claude as primary host, with multi-host opt-in".

---

## Adapter responsibilities

A host adapter in `hosts/<name>.ts` declares a `HostConfig`:

- **Install paths** for where skills go (`.claude/skills/`,
  `.openclaw/skills/`, etc.).
- **Available tools** — which of Claude's tool names have equivalents in
  this host. `AskUserQuestion`, `Bash`, `Edit`, `Read`, `Skill` are the
  core set most skills depend on.
- **Skill dispatch mechanism** — how a natural-language user request
  turns into a skill invocation on this host.

The adapter is consulted at install time (`./setup` writes skills to
the right paths with the right tool-allowlist) and at runtime
(preamble detects host and adjusts behavior where needed).

---

## What this rules out

- **Per-host skill variants.** We do not ship `/qa-claude`,
  `/qa-codex`, `/qa-openclaw`. One skill, multiple hosts.
- **Host-specific prose in skill bodies.** "In Claude Code, ..." is a
  smell. If the behavior differs, the adapter should abstract it; if
  it cannot, the skill should `AskUserQuestion` or detect at runtime.
- **Hardcoded tool names.** A skill that calls `ClaudeCode.thinkHard`
  or any tool not in the core set is non-portable and will fail in
  other hosts. Stick to the documented tool set.

---

## Where this intersects other decisions

- **Skill discovery** — host adapters determine where skills are
  installed; see
  **[skill-system.md](skill-system.md)**.
- **Browser daemon** — the daemon is host-agnostic (it is a localhost
  HTTP service), but pairing via `/pair-agent` specifically enables
  "a remote agent in a different host can drive my browser". See
  **[browser-daemon.md](browser-daemon.md)** and
  **[security-model.md](security-model.md)**.
