---
title: "Safety"
owners: []
soft_links: [/product/ethos.md, /skills/debug/NODE.md]
---

# Safety

Guardrails that protect the user from destructive operations and from
the agent accidentally editing the wrong files. The concern: when an
agent is working fast, it should not be able to cause unrecoverable
damage without the user noticing.

These are **guardrails**, not a sandbox — the user can always override.
The goal is "the user sees the intent before the action happens", not
"the agent is prevented from doing bad things".

---

## Member skills

- **`/careful`** — Warn before destructive shell commands. Advisory
  prose before `rm -rf`, `DROP TABLE`, `git push --force`,
  `git reset --hard`, `kubectl delete`, and similar. User can override
  each warning. Useful when touching prod or debugging live systems.
- **`/freeze`** — Hard-scope edit boundary. Blocks `Edit` and `Write`
  outside a specified directory for the rest of the session. Useful
  when debugging (you want to prevent "fixing" unrelated code) or
  when working inside a module whose contract must stay stable.
- **`/guard`** — Both together. `/careful` + `/freeze` in one
  command. Use when the session needs maximum safety (touching prod,
  debugging a live system).
- **`/unfreeze`** — Clears the freeze boundary. Re-enables writes
  outside the scope without ending the session.

---

## Shared decisions

- **Warn, do not block, for destructive commands.** The user is the
  authority. `/careful` surfaces the intent; the user decides. This is
  **User Sovereignty** (see
  **[/product/ethos.md](../../product/ethos.md)**) expressed as UX.
- **Blocking is scoped, not global.** `/freeze` restricts writes to a
  directory rather than disabling writes entirely. The user keeps
  working; only out-of-scope edits are blocked.
- **No persistent state between sessions.** Freeze and careful are
  per-session toggles. A new session starts with full surface. This
  is deliberate — a freeze that survives restart is a footgun.
- **Overrides are always possible.** Every guardrail has a documented
  override path. We are not writing a firewall; we are writing a
  seatbelt.

---

## When each applies

- `/careful` — shared-environment work, production databases, any
  session where the user has not had coffee yet.
- `/freeze` — debugging (to prevent "fixing" unrelated code), module
  boundary enforcement, refactors that must not spill over. `/investigate`
  auto-suggests `/freeze` as part of its methodology (see
  **[/skills/debug/NODE.md](../debug/NODE.md)**).
- `/guard` — production incidents, any session where both modes apply.
- `/unfreeze` — when freeze has served its purpose and the user wants
  the full edit surface back without restarting the session.

---

## Cross-references

- Code-level safety (SQL injection, trust boundaries, etc.) is
  **[/skills/review/NODE.md](../review/NODE.md)**, not this domain.
- Runtime safety for the browser daemon (tunnel exposure) is
  **[/architecture/security-model.md](../../architecture/security-model.md)**.
- Investigation methodology that pairs naturally with freeze:
  **[/skills/debug/NODE.md](../debug/NODE.md)**.
