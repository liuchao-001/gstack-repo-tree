---
title: "Learnings (Compound Memory)"
owners: []
soft_links: [/skills/debug/NODE.md, /architecture/skill-system.md]
---

# Learnings (Compound Memory)

Every gstack skill ends by writing a learning entry to the project's
local JSONL store. Every skill's preamble starts by reading the top-3
most relevant entries. Over time this produces "gstack learns your
codebase" behavior without any model training, fine-tuning, or
embeddings.

---

## The store

Path: `~/.gstack/projects/{project-slug}/learnings.jsonl`

One JSON line per learning. Each entry carries at minimum:

- **Skill name** that produced it.
- **Timestamp.**
- **Outcome** — success or failure (failures are richer; they are the
  expensive lesson).
- **Context** — what the skill was trying to do, what went wrong or
  right, and what the takeaway is.

The project slug is produced by `bin/gstack-slug` — deterministic
per-repo so learnings stay attached to the right project even across
directory moves.

---

## Load-time: preamble reads top-3

At the start of every skill run, the preamble queries the store for
the 3 most relevant learnings — scored by recency, skill overlap, and
textual similarity to the current task — and prepends them as
context. The agent sees "last time this project tried this, here is
what happened".

This is why a second try at a tricky bug tends to succeed: the agent
sees what did not work last time.

---

## Why failures matter more than successes

A success means "it worked once under these conditions". A failure
means "this pattern is wrong in this codebase, avoid it". Failures
carry more signal because they encode a constraint the code does not
express.

Preamble loading weights failures accordingly: a recent failure on
a similar task outranks an old success.

---

## Management via `/learn`

`/learn` (see **[/skills/debug/NODE.md](../skills/debug/NODE.md)**)
exposes the store:

- **Review** — show what has accumulated.
- **Search** — "did we fix this before?"
- **Prune** — remove stale entries (codebase has moved on, learning
  is no longer applicable).
- **Export** — share learnings across a team or archive them.

Without pruning, the store grows unbounded and stale entries dilute
the load-time signal. `/learn prune` is part of routine maintenance.

---

## What this is not

- **Not a vector database.** There is no embedding model, no
  similarity index, no RAG pipeline. The store is flat JSONL and
  ranking is heuristic.
- **Not shared between projects.** Each project slug has its own
  store. A learning from Project A does not leak into Project B.
- **Not a training corpus.** Nothing is sent to any model provider.
  The learnings stay local unless the user explicitly exports them.
- **Not a substitute for the tree.** The tree captures *decisions*
  that future agents need to know; learnings capture *operational*
  outcomes. A skill that should update the tree but instead logs a
  learning has mis-used the tool.

---

## Privacy

Learnings can include code snippets, file paths, error messages, and
diffs from the user's project. They stay on the user's machine by
default. Telemetry is separate (see
**[/infrastructure/telemetry.md](../infrastructure/telemetry.md)**)
and does not include learning content.
