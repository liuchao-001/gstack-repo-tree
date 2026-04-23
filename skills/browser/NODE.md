---
title: "Browser"
owners: []
soft_links: [/architecture/browser-daemon.md, /architecture/security-model.md, /skills/qa/NODE.md]
---

# Browser

The skills that sit directly on top of the persistent Chromium daemon.
They are the "give the agent eyes and hands" surface — everything
else (QA, design review, DX audit, canary) composes them.

The runtime architecture (why a daemon, ref system, command dispatch)
is in **[/architecture/browser-daemon.md](../../architecture/browser-daemon.md)**.
The security posture when a remote agent is involved is in
**[/architecture/security-model.md](../../architecture/security-model.md)**.
This node covers the user-facing skill surface.

---

## Member skills

- **`/browse`** — The headless browser tool. ~100 ms per command after
  daemon warm-up. 40+ commands (goto, click, fill, screenshot,
  console, cookies, tabs, chain, ...). Discoverable via `$B help`.
  This is the foundational skill everything else builds on.
- **`/open-gstack-browser`** — Headed mode with sidebar extension.
  AI-controlled Chromium window the user can watch in real time.
  Sidebar extension shows a live activity feed and chat. Anti-bot
  stealth built in. Auto-routes between Sonnet/Opus based on task
  complexity.
- **`/pair-agent`** — Cross-agent coordination. Lets a remote agent
  (OpenClaw, Hermes, Codex, Cursor, or anything that can hit HTTP)
  share the user's browser. Each remote agent gets its own scoped
  tab with read/write or admin token. Works with ngrok for off-
  machine agents.
- **`/setup-browser-cookies`** — Cookie import. Interactive picker
  UI to import cookies from Chrome / Arc / Brave / Edge into the
  daemon. macOS-only in V1 (Keychain unlock). Use before QA against
  authenticated pages.
- **`/connect-chrome`** — Symlink alias for `/open-gstack-browser`
  kept for backwards compatibility.

---

## Shared decisions

- **One daemon per workspace.** Every browser skill talks to the
  same daemon. Workspace isolation is real — two Conductor workspaces
  run two daemons on two ports with two state files.
- **Cold start is a one-time tax.** First browse call pays 2–3s;
  everything after is ~100 ms. Skills are written assuming warm
  daemon as the steady state.
- **Headed mode is a distinct surface.** `/open-gstack-browser`
  exists because the user sometimes needs to *see* the agent work.
  The extension + sidebar are a complete secondary product inside
  gstack, not a toggle on the headless browser.
- **Pairing is security-first.** `/pair-agent` generates scoped
  tokens per session; it does not hand out root access. The full
  command surface is local-only; tunnel callers get an allowlist.
  See **[/architecture/security-model.md](../../architecture/security-model.md)**.
- **Cookie import is opt-in and manual.** gstack never silently
  reads the user's keychain. `/setup-browser-cookies` launches an
  interactive picker and asks for Keychain approval per domain.
- **Voice triggers are aspirational.** Several browser skills list
  speech-to-text aliases ("open gstack browser", "launch chrome",
  "pair agent", "connect agent"). These are forward-compat — the
  V1 routing path is still the slash command. See
  **[/conventions/voice-triggers.md](../../conventions/voice-triggers.md)**.

---

## Cross-references

- Runtime internals:
  **[/architecture/browser-daemon.md](../../architecture/browser-daemon.md)**.
- Security (especially for remote pairing):
  **[/architecture/security-model.md](../../architecture/security-model.md)**.
- Primary users of these skills:
  **[/skills/qa/NODE.md](../qa/NODE.md)** (`/qa`, `/qa-only`),
  **[/skills/design/NODE.md](../design/NODE.md)** (`/design-review`),
  **[/skills/devex/NODE.md](../devex/NODE.md)** (`/devex-review`),
  **[/skills/release/NODE.md](../release/NODE.md)** (`/canary`).
