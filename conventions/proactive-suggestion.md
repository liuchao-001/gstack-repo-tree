---
title: "Proactive vs. Advisory Suggestion"
owners: []
soft_links: [/architecture/skill-system.md, /product/ethos.md, /skills/meta/NODE.md]
---

# Proactive vs. Advisory Suggestion

Many gstack skills describe themselves as "proactively suggest when..."
in their description. This is a user-level behavior controlled by a
single config flag, not a per-skill toggle.

---

## The two modes

- **Proactive (default)** — when the user's request matches a skill's
  purpose, the host agent invokes the skill directly via the `Skill`
  tool. No "should I run this?" prompt.
- **Advisory** — when the user's request matches a skill's purpose,
  the host agent says "I think `/<name>` would help here — want me to
  run it?" and waits for the user's approval.

The choice is set in `~/.gstack/config.yaml`:

```yaml
proactive: true   # or false
```

Skill preambles read this at runtime and branch behavior accordingly.
A user can flip the flag with `gstack-config set proactive false` and
every skill respects it immediately — no regeneration.

---

## Why the default is proactive

The thesis: if the user types "fix the failing deploy", they want the
deploy fixed, not a conversation about methodology. Proactive mode
treats intent matching as permission. It is the faster, less
conversational experience.

But proactive mode has a cost: a skill misread can waste a turn, and
novice users sometimes want the agent to confirm. Hence the escape
hatch.

---

## Why advisory is a first-class option, not an edge case

**User Sovereignty** (see
**[/product/ethos.md](../product/ethos.md)**) is one of the three
load-bearing principles. Users who prefer the "recommend, then wait"
experience are not wrong — they are running gstack differently. The
config lets them do that without every skill having its own "ask
first" flag.

---

## Authoring consequence

Skills author their descriptions as if proactive is the mode:

> "Proactively invoke this skill (do NOT answer directly) when the
> user reports errors, 500 errors, stack traces, ..."

That prose is read by the host agent's router. In proactive mode it
produces direct invocation. In advisory mode, preamble code rewrites
the invocation into a prompt-and-wait pattern. Skills do not have to
implement two paths themselves.

---

## `/plan-tune` interaction

`/plan-tune` (see **[/skills/meta/NODE.md](../skills/meta/NODE.md)**)
adds a finer layer: per-question preferences (never-ask, always-ask,
ask-only-for-one-way) on top of the global proactive toggle. V1 is
observational only; the per-question preferences are recorded but not
yet actively muting questions.

When per-question tuning does go active, proactive mode will remain
the outer default and per-question preferences the inner override.

---

## What this does not cover

- **Destructive commands.** Proactive mode does not mean auto-running
  `rm -rf`. Guardrails in **[/skills/safety/NODE.md](../skills/safety/NODE.md)**
  apply regardless of proactive setting.
- **Explicit slash-command invocation.** When the user types
  `/<name>` explicitly, the skill runs — proactive mode is about
  inferred invocation, not explicit.
