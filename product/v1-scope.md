---
title: "V1 Scope"
owners: []
soft_links: [/architecture/browser-daemon.md, /skills/NODE.md]
---

# V1 Scope

What gstack 1.x does and does not attempt. Version is tracked at
`VERSION` in the source repo (currently in the `1.6.x.x` line); this
document changes when the product boundary changes, not when features
are added within it.

---

## In scope

### Workflow coverage
The **Think → Plan → Build → Review → Test → Ship → Reflect** pipeline,
end-to-end, inside a single Claude Code session or Conductor workspace.
Every stage has at least one skill that owns it (see
**[/skills/NODE.md](../skills/NODE.md)**).

### Single-user, single-workspace model
Each daemon serves one user in one working directory. No multi-tenant
isolation, no shared session state across users. Conductor is the
answer for "multiple workspaces" — it runs N isolated single-user
setups in parallel, not a shared N-user setup.

### macOS-first
Cookie import, browser launch hooks, and some native integrations
(Keychain) are macOS-only in V1. Linux is best-effort for CLI skills;
Windows is not supported.

### Browser-centric QA and review
The persistent Chromium daemon (see
**[/architecture/browser-daemon.md](../architecture/browser-daemon.md)**)
is load-bearing. Skills that need "eyes on the product" (`/qa`,
`/design-review`, `/devex-review`, `/canary`) all use it.

### Claude as primary host, with multi-host opt-in
Claude Code is the reference host. Other hosts (Codex, OpenClaw, Cursor,
Kiro, etc.) are supported via `hosts/` adapters but are not the
promised experience. A skill that works only in Claude and "might work
elsewhere" is acceptable; a skill that works only in a non-Claude host
is not.

---

## Deliberately out of scope

### Multi-user / team collaboration inside one workspace
The path forward is per-user workspaces + shared tree repos, not
multi-user session state. This is why Conductor + Context Tree exist as
separate primitives.

### Cross-platform cookie decryption
Chrome's encrypted-cookie format differs per OS. V1 implements macOS
(Keychain). Linux/Windows are explicit non-goals — users on those
platforms do manual login.

### Iframe auto-discovery in the browse daemon
The ref system (`@e1`, `@c1`) works on the top-level frame. Iframe
traversal exists as an escape hatch but is not a first-class feature.

### Voice input as a shipping feature
Every skill declares `voice triggers` in front-matter, but V1 does not
ship a speech-to-text pipeline. The aliases are a forward-compatibility
contract for a future release.

### Automatic billing / usage caps
Evals tier 2/3 cost real money (~$3.85 + $0.15 per run). V1 gates these
behind `EVALS=1` / diff-based selection, not a per-user budget enforcer.

---

## Target users

1. **Solo founders / CEOs shipping product.** The "20-person team replaced
   by one person + agents" thesis. Garry Tan himself is the archetype.
2. **First-time Claude Code users who want structure.** The skill library
   replaces "what do I even type" with "pick a slash command".
3. **Tech leads automating review and QA.** The review / codex / cso /
   qa cluster is the draw; the rest is bonus.

Not targeted: beginners learning to code (gstack assumes an engineer who
can read and approve diffs), enterprise teams needing SSO and audit
logs, non-English-first workflows (skill prose is English-native).

---

## Version scheme

`X.Y.Z.W` — major.minor.patch.revision. Every feature branch bumps its
own version and writes a CHANGELOG entry covering **all** commits in the
branch, not just the latest. See
**[/conventions/versioning.md](../conventions/versioning.md)**.
