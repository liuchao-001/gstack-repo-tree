---
title: "Debug"
owners: []
soft_links: [/product/ethos.md, /skills/safety/NODE.md, /conventions/learnings.md, /conventions/checkpointing.md]
---

# Debug

Root-cause investigation, memory management, and session persistence.
The shared concern: make debugging systematic rather than vibes-based,
and make "what did we try and learn" visible across sessions instead
of evaporating when the terminal closes.

---

## Member skills

- **`/investigate`** — Systematic debugging with root-cause focus.
  Four phases: investigate, analyze, hypothesize, implement. Iron Law:
  no fixes without a stated root cause. Auto-suggests `/freeze` to
  scope the session to the suspect module. Gives up and escalates
  after 3 failed fixes rather than thrashing.
- **`/learn`** — Manage project learnings. Review, search, prune,
  export what gstack has learned across sessions (the
  `~/.gstack/projects/{slug}/learnings.jsonl` store). Useful when
  the agent starts hallucinating patterns or when you want to see
  "what did we already fix in this codebase".
- **`/context-save`** — Save working context. Captures git state,
  decisions made, approaches tried, remaining work. Pair with
  `/context-restore` to resume later.
- **`/context-restore`** — Restore context saved earlier. Loads the
  most recent state, even across Conductor workspace handoffs.
  Renamed from `/checkpoint resume` because Claude Code claimed
  `/checkpoint` as a native rewind alias.

---

## Shared decisions

- **No fix without root cause.** `/investigate`'s Iron Law. A
  "patch it and see" fix that ships is a recurrence waiting to
  happen. This is **Search Before Building** (see
  **[/product/ethos.md](../../product/ethos.md)**) applied to bugs.
- **Stop after 3 failed fixes.** Thrashing on the same symptom
  produces more damage than the original bug. `/investigate`
  escalates to the user rather than keep trying.
- **Learnings compound across sessions.** `/learn` exposes the
  store; every skill's preamble loads the top-3 most relevant
  learnings on start. See
  **[/conventions/learnings.md](../../conventions/learnings.md)**.
- **Context is a commit, not a note.** `/context-save` writes to a
  checkpoint file + optional WIP commit with structured body; it
  is not a free-text scratchpad. This makes "restore" reliable
  and survives process kills. See
  **[/conventions/checkpointing.md](../../conventions/checkpointing.md)**.
- **Conductor-aware.** `/context-restore` looks across branches and
  workspaces so a Conductor hand-off does not lose state.

---

## When each applies

- `/investigate` — errors, 500s, stack traces, "it was working
  yesterday", any troubleshooting. Proactively invoked when the
  user reports a broken behavior.
- `/learn` — debugging a recurring class of bug ("didn't we fix this
  before?"), pruning stale learnings, exporting learnings to share.
- `/context-save` — end of a long session, before a Conductor
  hand-off, before a destructive operation, before lunch.
- `/context-restore` — start of a continuation session, resuming
  after a crash or restart.

---

## Cross-references

- Safety guardrails that pair with investigation:
  **[/skills/safety/NODE.md](../safety/NODE.md)**.
- How learnings are loaded and stored:
  **[/conventions/learnings.md](../../conventions/learnings.md)**.
- How checkpointing interacts with the release pipeline (`/ship`
  filter-squashes WIP commits):
  **[/conventions/checkpointing.md](../../conventions/checkpointing.md)**.
