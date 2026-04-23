---
title: "Design System"
owners: []
soft_links: [/skills/design/NODE.md]
---

# Design System

Aesthetic decisions that unify what gstack outputs — terminal prose,
design mockups, generated HTML, PDFs, sidebar UI. Canonical source is
`DESIGN.md` in the source repo.

---

## Aesthetic: Industrial / Utilitarian

**Decision:** The CLI heritage is the brand. Do not dress it up as a
consumer SaaS.

- Dark-mode default, near-black base (`#0C0C0C`).
- Amber-500 accent (the "terminal cursor" warm tone) for state and focus.
- Chrome (nav, frames, borders) stays neutral; data gets color.
- Density: comfortable, not cramped. Not iOS-style generous whitespace.

**How to apply:** When a skill generates a mockup, HTML page, PDF, or
status readout, default to dark/neutral with amber accents. Light mode
is an option, not the starting point.

**What this rules out:** Pastel gradients, glassmorphism, playful
illustrations, Lottie animations, Comic-Sans-adjacent friendliness. The
product is for people who live in terminals.

---

## Typography

- **Hero / headings:** Satoshi
- **Body:** DM Sans
- **Mono / code / personality:** JetBrains Mono

The personality font is intentional. Mono is not just "for code" — it is
used in UI chrome where the "I am a developer tool" signal matters.
Resist the instinct to replace JetBrains Mono with a proportional font
in headings "for readability"; the tradeoff is wrong for this audience.

---

## Voice

Applies to skill output, CHANGELOG entries, release summaries, and any
prose gstack generates.

- **No em-dashes.** An em-dash is the AI-prose tell. Use a period, a
  comma, parentheses, or restructure.
- **No AI vocabulary.** "Leveraging", "robust", "seamlessly", "best-in-class",
  "comprehensive" — strip them. Write like a person who ships.
- **Real numbers over superlatives.** "810× productivity" is concrete;
  "dramatically faster" is not.
- **Short paragraphs.** A user scanning a terminal wants to see the
  answer, not an essay.
- **Directness over politeness.** "This is wrong because X" beats "You
  might consider reconsidering whether X could be a concern here."

See also **[/conventions/community-guardrails.md](../conventions/community-guardrails.md)**
for why external contributors cannot "professionalize" this voice.

---

## Where this applies

- **Skill output in the terminal** — headers, progress lines, summaries.
- **CHANGELOG / release notes** — 2-line bold headline, lead paragraph,
  metrics table, "what this means" closer.
- **Generated HTML pages** (`/design-html`, `/make-pdf`) — Pretext layout,
  zero JS deps, computed text reflow.
- **Sidebar UI** in the browser extension — industrial, not consumer.

The system is applied by the skills listed in
**[/skills/design/NODE.md](../skills/design/NODE.md)**. Non-design skills
still inherit the voice rules when they produce prose.
