---
title: "Telemetry"
owners: []
soft_links: [/product/ethos.md, /conventions/learnings.md]
---

# Telemetry

gstack collects anonymized usage telemetry via Supabase. It is **off
by default**, opt-in, and scoped to a short list of event types.
Trust is hard to earn and easy to lose; this page records the
design choices that make the telemetry defensible.

---

## What is collected

When the user opts in, the store records three kinds of events:

1. **Installations** — user ID (random UUID, not GitHub login),
   platform (macOS/Linux), gstack version.
2. **Skill events** — which skill ran, how long it took,
   success/failure outcome. No skill arguments, no file paths, no
   prompt content.
3. **Attack telemetry** — prompt-injection attempts detected by the
   sidebar-agent (domain hash, not full URL; timestamp; attack
   classification).

---

## What is explicitly **not** collected

- Source code, file contents, diffs.
- File paths or repository names.
- Prompt content sent to any model.
- Model responses.
- Browser cookies, form data, or any browser state.
- IP addresses beyond what the edge-function request log
  unavoidably captures (and Supabase rotates).

This list is load-bearing. If a future change would add any of the
above to telemetry, it is a product decision that requires founder
approval — not a routine engineering change.

---

## Why edge functions gate writes

Supabase is configured with Row-Level Security denying direct
inserts from clients. Every telemetry event goes through an edge
function that:

- Validates event schema (required fields, types, length limits).
- Checks event type against an allowlist.
- Rejects oversized events silently (no retry storm).

Without this layer, a misbehaving gstack install could write
arbitrary data into telemetry and there would be no constraint on
"what if the client lies about what it is sending". The edge
function is the enforcement boundary.

---

## Why opt-in, not opt-out

**User Sovereignty** (see **[/product/ethos.md](../product/ethos.md)**)
as applied to privacy. Default-on telemetry is a dark pattern; default-
off with a clearly explained opt-in is not. The adoption cost is
real (we get fewer events) and deliberate.

---

## Relationship to learnings

Learnings (see **[/conventions/learnings.md](../conventions/learnings.md)**)
and telemetry are distinct systems:

| | Learnings | Telemetry |
|---|-----------|-----------|
| Where stored | Local JSONL | Remote Supabase |
| Content | Detailed outcomes, possibly with code | Anonymized events only |
| Default | On | Off |
| Purpose | Future sessions read and act on them | Operators see usage patterns |

Learnings never leave the user's machine. Telemetry never includes
learning content. Crossing the streams is explicitly not part of
the design.

---

## Where to find current schema

Source repo: `supabase/migrations/`. The migrations are the
authoritative schema. If the shape changes and this document has
not been updated, this document is the stale one — but the *intent*
documented here (what is and is not collected) is the constraint
the schema must honor.
