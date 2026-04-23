---
title: "Checkpointing (WIP Commits + Filter-Squash)"
owners: []
soft_links: [/skills/debug/NODE.md, /skills/release/NODE.md]
---

# Checkpointing (WIP Commits + Filter-Squash)

Long sessions can crash, be interrupted, or hand off to another
workspace. Losing an hour of progress because the process died is
unacceptable. Checkpointing is the answer: continuous auto-commits
during work, filter-squashed out before the PR ships.

---

## Continuous checkpoint mode (opt-in)

When `CHECKPOINT_MODE=continuous` is set in
`~/.gstack/config.yaml`, gstack auto-commits at every safe point
during a session:

- Each commit message is prefixed `WIP:`.
- The body has a structured `[gstack-context]` block with
  machine-readable metadata (skill, phase, decision trail).

These commits make `git reflog` and `git bisect` effective even on
work-in-progress. They also enable `/context-restore` to reconstruct
session state from the branch history, not just from a notes file.

---

## `/context-save` vs. continuous mode

Two complementary mechanisms:

- **`/context-save`** — explicit, user-triggered. Captures git state
  + decisions + failed approaches to a checkpoint file. Good for
  "end of day" or "before the weekend".
- **Continuous mode** — implicit, skill-triggered. Auto-commits at
  safe points so a crash mid-session loses minutes, not hours. Good
  for long investigative or build sessions.

Most users run without continuous mode and use `/context-save`
explicitly. Power users running long sessions enable continuous.

---

## Filter-squash at `/ship` time

A PR full of `WIP:` commits is unreadable. So `/ship` filter-squashes
the WIP commits before pushing:

- WIP commits with the `[gstack-context]` marker are squashed into
  the preceding real commit.
- Non-WIP commits pass through untouched.
- The resulting branch has clean, logical commits ready for review.

The filter is precise: it squashes only commits whose structured
body matches the gstack checkpoint format. A developer's own WIP
commits (no marker) are not touched — gstack only cleans up its own
noise.

---

## Why this approach

Alternatives considered and rejected:

- **Don't auto-commit; use `git stash`.** Stash is opaque, easy to
  lose, and does not survive process kills well. Branch history is
  more durable.
- **Auto-commit to a separate branch.** Adds branch management
  overhead; `/ship` still has to merge and clean up. Same
  complexity, more moving parts.
- **Write state to a side-file (no git).** State file loses the
  diffable property that makes `/context-restore` work across
  workspaces.

The branch-with-cleanup approach gives durability (git) +
reviewability (`/ship` cleans up) without new primitives.

---

## Interactions with other skills

- **`/investigate`** — benefits most from continuous mode. A
  hypothesis-testing session with 10 failed attempts is recoverable
  from history.
- **`/ship`** — owns the filter-squash step. Do not squash manually
  before running `/ship`; it knows what to keep.
- **`/context-restore`** — can replay both explicit saves and the
  structured WIP commits to reconstruct where the session was.

---

## What this does not cover

- **Remote-branch pushes during WIP.** Continuous mode commits
  locally; it does not push. A user who has `git push`ed a WIP
  branch has to clean up manually (or use `/ship`, which handles
  the force-push after squashing safely).
- **Interactive rebases the user started.** If the user is mid-rebase,
  continuous mode pauses — it does not interfere with hand-edits.
