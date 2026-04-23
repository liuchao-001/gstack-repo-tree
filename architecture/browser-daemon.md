---
title: "Browser Daemon"
owners: []
soft_links: [/skills/browser/NODE.md, /skills/qa/NODE.md, /architecture/security-model.md]
---

# Browser Daemon

The persistent headless Chromium process that backs `/browse`,
`/open-gstack-browser`, and every skill that needs "eyes on the
product" (`/qa`, `/design-review`, `/devex-review`, `/canary`). This is
the single most load-bearing piece of infrastructure in gstack — if it
is slow or stateful in the wrong ways, the QA unlock evaporates.

---

## Why persistent

Playwright cold-starts Chromium in 2–3 seconds. A QA session makes
20–50 commands. That is 40–150 seconds of pure cold-start overhead per
session, and — worse — cookies, tabs, and local storage reset on every
call, breaking any test that depends on being logged in.

**The decision:** run one Chromium per workspace, long-lived, and talk
to it over localhost HTTP. First call pays the cold-start; every
subsequent call is ~100–200 ms. State is preserved across commands,
which is the property that makes real-browser QA usable at all.

This is the trade: memory footprint (one Chromium resident per
workspace) for latency + state. Given gstack's target user (solo
builder on a laptop), memory is cheap and latency is not.

---

## State file: `.gstack/browse.json`

Each workspace has a state file that records:

- **PID** of the running daemon
- **Port** (random in 10000–60000, chosen at spawn)
- **UUID token** for authentication (see
  **[security-model.md](security-model.md)**)
- **Binary version hash** so stale daemons from an older release can be
  detected and replaced

The CLI's startup check: read the state file, GET `/health`, verify
version hash matches. If any check fails, kill and respawn. The
workspace never has two daemons at once.

**Consequence:** the state file is per-workspace, not global. Two
workspaces have two daemons. This is why multi-workspace parallelism
(Conductor) works — there is no shared state to corrupt.

---

## Lifecycle

- **Auto-start on first use.** No `gstack daemon start` command is
  needed. The first browse CLI call that doesn't find a healthy daemon
  spawns one.
- **30-minute idle timeout.** If no command has hit the daemon in 30
  minutes, it exits cleanly. Next call spawns a fresh one.
- **Auto-restart on version mismatch.** When gstack upgrades, the
  next CLI call sees the binary version hash change and transparently
  replaces the running daemon.

Users do not manage daemon lifecycle. The fact that a daemon is even
involved is an implementation detail that should only surface when
something has gone wrong.

---

## Ref system (`@e1`, `@c1`)

Elements are addressed by short refs rather than CSS selectors or XPath:

- `@eN` — element from the accessibility tree (screen-reader semantics).
- `@cN` — cursor-interactive element (buttons, links, inputs).

Refs come from a snapshot the agent requests; each subsequent action
(`click @c2`, `fill @e5 "hello"`) re-resolves the ref against the
current page via Playwright's `getByRole()` Locator API. No DOM
injection — CSP-safe. Stale-ref detection runs `locator.count()` before
acting (~5 ms overhead). Refs are invalidated on every `framenavigated`
event, forcing a fresh snapshot.

**Why refs instead of selectors:** agents are bad at writing
selectors. They guess, they fail, they retry. Refs are derived from
what the agent already asked for (the snapshot), so they are by
construction pointing at something real. When they break (navigation,
re-render), the system says so instead of silently clicking the wrong
element.

---

## Command dispatch: READ / WRITE / META

Commands are split into three categories with different semantics:

| Category | Examples | Retry-safe? |
|----------|----------|-------------|
| **READ** | text, html, console, cookies, screenshot | Yes |
| **WRITE** | goto, click, fill, press, check | No (side effects) |
| **META** | snapshot, tabs, chain, help | Yes (server-side) |

The agent is told which category each command is so it can retry
appropriately. The distinction also gates security: in tunnel mode
(see **[security-model.md](security-model.md)**), only a scoped subset
of READ/META is exposed.

---

## What this does not solve

- **Iframe traversal.** Ref discovery works on the top-level frame.
  Iframes have an escape hatch but are not first-class. A skill that
  needs deep iframe support has to do its own work.
- **Authentication at page load time.** Cookie import (via
  `/setup-browser-cookies`) is a separate flow; the daemon does not
  decrypt platform keychains on its own.
- **Visual regression.** The daemon can screenshot, but diffing is a
  skill-level concern, not a daemon feature.
- **Sandboxing.** The daemon runs as the user. Do not send untrusted
  URLs to it; this is not a hardened browser.
