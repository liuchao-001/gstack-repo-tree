---
title: "Ethos"
owners: []
soft_links: [/conventions/community-guardrails.md]
---

# Ethos

gstack is built on three philosophical commitments. They are stated
directly in `ETHOS.md` in the source repo and are explicitly off-limits
to external rewrites (see
**[/conventions/community-guardrails.md](../conventions/community-guardrails.md)**).
New skills and workflows that contradict these are rejected — they are
the product, not decoration.

---

## 1. Boil the Lake

**Decision:** Complete the whole problem, not a slice.

Traditional engineering triages ruthlessly because people are scarce. With
AI, the marginal cost of completeness is near-zero — the agent can do the
last 10% for roughly the cost of the first 90%. So gstack skills aim for
the full thing: full test coverage, full docs update, full design
coherence, full release note.

**How to apply:** When a skill has to choose between "ship the core" and
"ship the core + the ancillaries that usually get deferred", favor the
second unless there is a specific blocking reason. `/ship` auto-invokes
`/document-release`; `/qa` iterates until nothing else fails; `/design-review`
keeps fixing until the visual audit is clean.

**What this rules out:** "Good enough" as a shipping standard. A `/ship`
that skips CHANGELOG because "the commits explain it" is a regression.

---

## 2. Search Before Building

**Decision:** Three layers of knowledge; the deeper, the more valuable.

1. **Surface:** What the current codebase already does (grep, read).
2. **Prior art:** What the community has already solved (libraries, patterns,
   blog posts).
3. **First principles:** Why this problem exists and what the underlying
   constraint is.

Layer 3 is prized above the others. A skill that derives the answer from
first principles produces better output than a skill that pattern-matches
existing code.

**How to apply:** `/investigate`'s Iron Law ("no fixes without root cause")
is this principle expressed as policy. `/office-hours` forcing questions
are this principle applied before code is written. `/review` and `/codex`
are this principle applied after: did we verify the assumption, or did we
just ship the first plausible thing?

**What this rules out:** Cargo-culting. A skill that says "this matches
the framework convention, therefore ship" without asking whether the
convention is right here is incomplete.

---

## 3. User Sovereignty

**Decision:** AI recommends, the user decides.

Skills can recommend aggressively — proactive suggestion is the default
(see **[/conventions/proactive-suggestion.md](../conventions/proactive-suggestion.md)**).
But when a user's stated direction contradicts what the skill would
otherwise do, the user wins. Skills must not auto-apply decisions the
user has actively pushed back on.

**How to apply:** `AskUserQuestion` is the escape hatch built into every
interactive skill. `/plan-tune` exists specifically so users can declare
"stop asking me this" at a global level. The `/careful` / `/freeze` /
`/guard` family is this principle applied to destructive commands —
warn, but let the user override.

**What this rules out:** Skills that "know better" than the user and
silently override their direction. If a reviewer agent disagrees with
a decision, it comments; it does not force-push a fix.

---

## Why these three, together

- **Boil the Lake** without **Search Before Building** produces polished
  cargo-cult: every corner complete, but solving the wrong problem.
- **Search Before Building** without **User Sovereignty** produces
  paralysis: the agent keeps investigating instead of letting the user
  ship.
- **User Sovereignty** without **Boil the Lake** produces half-finished
  work: the user said "just the core, please" and the skill obeyed even
  when the ancillaries were free.

The combination is the point.
