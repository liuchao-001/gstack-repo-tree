---
title: "Voice Triggers"
owners: []
soft_links: [/architecture/skill-system.md, /product/v1-scope.md]
---

# Voice Triggers

Every gstack skill declares a `triggers` list in its front-matter —
natural-language aliases like "quality check", "test the app",
"upgrade the tools". This is forward-compatibility scaffolding, not a
V1 feature.

---

## What they are

A short list of phrases that should route to this skill if a
speech-to-text pipeline ever feeds input into the host agent.
Examples from existing skills:

- `/qa` → "quality check", "test the app", "run QA"
- `/cso` → "see-so", "see so", "security review", "security check"
- `/make-pdf` → "make it a pdf", "turn this markdown into a pdf"
- `/gstack-upgrade` → "upgrade the tools", "gee stack upgrade", "g
  stack upgrade"

The "see-so" / "gee stack" entries are explicit phonetic variants for
speech-to-text systems that mis-transcribe acronyms.

---

## Why V1 still declares them

Two reasons:

1. **Authoring them while building the skill is cheap.** Trying to
   backfill the phrase list across 40+ skills later would be a boring
   migration. Writing them at authoring time is almost free.
2. **The host agent's description-matcher already uses them.** Even
   without speech-to-text, the alias list is one more signal the
   router has when matching a typed natural-language request to a
   skill. "run QA on this" → `/qa` because "run QA" is in the
   triggers list.

---

## Why they are not the primary routing path in V1

The authoritative router is still the skill's `description` field +
the user's explicit `/<name>` invocation. Triggers are a tie-breaker,
not the mechanism. See **[/product/v1-scope.md](../product/v1-scope.md)**
("Voice input as a shipping feature" is out of V1).

This matters when authoring: do not put essential routing logic in
triggers alone. A skill whose description is vague and whose triggers
are the only discriminator is fragile.

---

## Convention for authoring

- **3–5 phrases max.** More than that becomes noise and dilutes the
  routing signal.
- **Include obvious phonetic failure modes** for acronym-heavy skill
  names (`/cso`, `/qa`, `/dx`).
- **Natural English, not jargon.** "Run QA" beats "Execute the QA
  workflow".
- **No overlap with other skills' triggers.** Duplicates create
  routing ambiguity.

---

## Future direction

When voice input does ship (outside V1), triggers become active. The
conservatism now (don't over-trigger, keep lists short) pays off
then — a skill with 15 aggressive triggers will over-fire; a skill
with 4 focused ones will behave.
