---
title: "Security Model"
owners: []
soft_links: [/architecture/browser-daemon.md, /skills/browser/NODE.md]
---

# Security Model

Covers only the browser daemon's exposure surface. Other security
concerns (OWASP/STRIDE review of generated code, prompt-injection
handling in skills) are handled by the `/cso` and `/review` skills and
are not architecture-level decisions.

---

## Threat model

The browser daemon serves two classes of caller:

1. **Local caller on the same machine** (the user's Claude Code session,
   or another local agent). Trusted.
2. **Remote caller** (a remote agent paired via `/pair-agent`, reaching
   the daemon through an ngrok tunnel). **Not** trusted — any mitigation
   that depends on "the caller is our agent" is wrong here.

The daemon's full command surface is safe for case 1 and dangerous for
case 2. The security model is about keeping case 2 bounded without
making case 1 awkward.

---

## Dual-listener design

When pairing activates an ngrok tunnel, the daemon does **not** add
header checks to its one listener. It binds two listeners:

- **Local listener** on `127.0.0.1:<random-port>`. Full command
  surface. Never reachable from the tunnel.
- **Tunnel listener** on a separate port, exposed via ngrok. Only a
  scoped allowlist is routed:
  - `POST /connect` — handshake
  - `POST /command` — scoped commands only, requires scoped token
  - `POST /sidebar-chat` — message passthrough to the sidebar UI

Root tokens sent over the tunnel return `403`. There is no way for a
tunnel caller to reach the full surface short of a bug in the tunnel
listener's code — which we can audit at one place.

**Why physical port separation, not header inference:** ngrok rewrites
headers. Historically we tried deciding "is this call from the tunnel"
by inspecting `Forwarded:` / `X-Real-IP:` / `X-Forwarded-For:`. This was
unreliable — header behavior changed between ngrok versions, and a
misconfiguration silently opened the full surface to the public
Internet. Binding two ports and routing them separately is
impossible-to-misconfigure in the same way.

---

## Tokens

- **Root token** — UUID in the workspace state file, generated on
  daemon spawn. Local-only. Gives access to every command.
- **Scoped tokens** — minted per-pair-agent session. Each token
  carries:
  - An expiration.
  - A command allowlist (typically READ + screenshot + tab info; no
    `goto`, no `fill`, no `click` unless explicitly granted).
  - An optional tab restriction (some sessions only see one tab).

Scoped tokens are what the remote agent receives. The root token never
leaves the user's machine.

---

## Why this lives in the tree

This is the kind of decision that is invisible until someone proposes
"let's add header-based auth for remote agents — it's simpler" and
re-introduces the exact problem the dual-listener design was built to
prevent. The tree records *why* the current design exists so future
proposals can be evaluated against the actual constraint.

Implementation lives in `browse/src/server/` and
`extension/background/` — read there when you need the code, not the
decision.

---

## What this does not cover

- **Prompt-injection defense in skills.** A reviewed skill can be
  tricked by adversarial PR content. That is handled at the skill
  level by **[/skills/review/NODE.md](../skills/review/NODE.md)** and
  **[/skills/safety/NODE.md](../skills/safety/NODE.md)**, not here.
- **Cookie / credential leakage via screenshots.** If the daemon
  screenshots a logged-in page and sends it to a remote agent, the
  remote agent sees the session. Scoped tokens typically disallow
  screenshot in pair mode for exactly this reason, but the
  enforcement is command-allowlist, not sandboxing.
- **Supply-chain security of skills themselves.** `/cso` covers this
  at audit time.
