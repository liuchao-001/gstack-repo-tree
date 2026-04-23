---
title: "Meta"
owners: []
soft_links: [/architecture/skill-system.md, /conventions/proactive-suggestion.md]
---

# Meta

Skills *about* gstack itself — tuning the agent's behavior, upgrading
the installation. These exist because gstack is a long-lived tool that
users configure to their preferences and upgrade over time.

---

## Member skills

- **`/plan-tune`** — Self-tuning question sensitivity and developer
  psychographic profile. Reviews which `AskUserQuestion` prompts fire
  across gstack skills, sets per-question preferences (never-ask,
  always-ask, ask-only-for-one-way), inspects the dual-track profile
  (what the user declared vs. what behavior suggests), enables or
  disables question tuning. **V1 status: observational only** — the
  profile is built but not yet used to mute questions automatically.
- **`/gstack-upgrade`** — Upgrade to the latest version. Detects
  global vs. vendored install, runs the upgrade, runs migrations,
  shows what is new.

---

## Shared decisions

- **User preferences persist in config, not in memory.**
  `~/.gstack/config.yaml` holds the long-lived toggles. Skills read
  them each run; the user can flip a flag and every skill respects it
  immediately without regeneration. See
  **[/architecture/skill-system.md](../../architecture/skill-system.md)**.
- **Tuning is observational before it is active.** V1 records which
  questions fire and how often; V1.1+ may auto-mute. Deliberately
  slow — a tuning system that silences the wrong question is worse
  than one that over-asks.
- **Upgrade must preserve user customizations.** Global vs. vendored
  install detection exists because users do both. A naive upgrade
  that blows away a vendored install is a bug.
- **Migrations run on upgrade.** Config schema changes, learnings
  store format changes, etc. are migrated forward. The user does not
  have to edit files by hand.

---

## Cross-references

- The proactive/advisory pattern that `/plan-tune` modulates:
  **[/conventions/proactive-suggestion.md](../../conventions/proactive-suggestion.md)**.
- What a skill is, and the config hooks skills read:
  **[/architecture/skill-system.md](../../architecture/skill-system.md)**.
