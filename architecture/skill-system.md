---
title: "Skill System"
owners: []
soft_links: [/skills/NODE.md, /conventions/proactive-suggestion.md]
---

# Skill System

**What a skill is:** a Markdown file (`SKILL.md`) with YAML front-matter
that Claude Code reads at runtime when the user types the matching slash
command. That is the entire contract. Skills are not plugins, not
subprocess tools, and not API clients — they are prose + bash blocks
that the host agent executes.

Everything else on this page exists to keep that contract robust as the
skill library grows.

---

## Authoring contract

Every skill directory has a `SKILL.md` (generated) and usually a
`SKILL.md.tmpl` (hand-edited). Front-matter declares:

- **name** — the slash command.
- **preamble-tier** (1–4) — which preamble block is injected (controls
  how much session setup runs).
- **description** — one sentence the host uses to route natural-language
  requests to the skill.
- **allowed-tools** — which Claude tools the skill can call.
- **triggers** — voice/natural-language aliases (forward-compat; not
  used as the primary routing signal in V1).

The body is hand-written prose with embedded bash blocks. Each bash
block is self-contained: no cross-block shell state, no expectation of
variables surviving between steps. A skill that relies on a variable
set in an earlier block is a bug waiting to happen.

---

## Template placeholders

Skills use `{{PLACEHOLDER}}` substitution to share content without
copy-paste. Substitution happens at **build time** (`bun run
gen:skill-docs`), not at runtime — the generated `SKILL.md` is
committed to the repo. Placeholders include:

- `{{PREAMBLE}}` — session setup, learnings load, telemetry, config
  read. Chosen by `preamble-tier` front-matter.
- `{{COMMAND_REFERENCE}}` — auto-extracted from `browse/src/commands.ts`
  so the browse CLI reference never drifts from the code.
- `{{QA_METHODOLOGY}}`, `{{DESIGN_METHODOLOGY}}` — reusable prose blocks
  shared across skill groups.
- `{{BASE_BRANCH_DETECT}}` — dynamic base-branch detection logic for
  PR-oriented skills.

**Why build-time:** runtime substitution would mean every session
re-renders every skill, slowing cold-start and making `SKILL.md` a
liar (the file on disk is not what the agent sees). Build-time
substitution keeps the file authoritative and makes diffs reviewable.

**Consequence:** CI has to verify the generated file is fresh. The
`skill-docs.yml` workflow runs `gen:skill-docs --dry-run` and fails
the PR if `git diff` is non-empty. Contributors must commit both the
template edit and the regenerated output.

---

## Preamble contract

The preamble is shared infrastructure every skill inherits. It is
responsible for:

1. **Session identification** — tag logs with a stable session ID so
   learnings can be attributed back.
2. **Learnings load** — read top-3 learnings from the project's local
   JSONL and prepend them as context.
3. **Telemetry (opt-in)** — if the user opted in, emit the skill-start
   event. Telemetry is off by default; see
   **[/infrastructure/telemetry.md](../infrastructure/telemetry.md)**.
4. **Config read** — load `~/.gstack/config.yaml` for user-level
   toggles (`PROACTIVE`, `CHECKPOINT_MODE`, `VENDOR_CHECK`,
   `SKILL_PREFIX`).

Skills read these config values at runtime to adjust behavior — not the
template engine. This means a user can flip a config flag and skills
respect it immediately, no regeneration required.

---

## Learnings

Every skill ends by logging an operational event to
`~/.gstack/projects/{slug}/learnings.jsonl`. Failures are richer than
successes. Next session, the preamble loads the top-3 most relevant
learnings and shows them as context. Over time this makes gstack
"learn your codebase" — without training, fine-tuning, or embeddings.
See **[/conventions/learnings.md](../conventions/learnings.md)** for
the pattern and its constraints.

---

## Discovery and routing

- Skills install to `~/.claude/skills/` (global) or `.claude/skills/`
  (project-local). Project-local overrides global so teams can pin a
  specific skill version for a repo.
- The host agent routes user input to a skill by matching its
  description + trigger metadata. In Claude Code this is native skill
  dispatch; in other hosts it goes through the
  **[host-integration.md](host-integration.md)** adapter.
- **Proactive vs. advisory invocation** is a user-level preference
  exposed in config and read at runtime. Default is proactive. See
  **[/conventions/proactive-suggestion.md](../conventions/proactive-suggestion.md)**.

---

## What the skill system is not

- **Not a plugin API.** Skills cannot register handlers, mutate the
  host runtime, or call into each other's internals. They invoke each
  other through the same slash-command surface the user sees.
- **Not a sandbox.** Bash in a skill runs in the user's shell with the
  user's permissions. `allowed-tools` gates Claude tool calls, not
  shell commands.
- **Not versioned per-skill.** gstack ships as one monolithic package
  with a single `VERSION`. There is no "this skill is v2 but the rest
  are v1". See **[/conventions/versioning.md](../conventions/versioning.md)**.
