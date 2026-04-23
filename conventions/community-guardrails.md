---
title: "Community Guardrails"
owners: []
soft_links: [/product/ethos.md, /product/design-system.md]
---

# Community Guardrails

gstack is open source and accepts external PRs. This document records
three categories of change that **require explicit founder approval**
and must not be merged on reviewer discretion alone. These are not
mechanical concerns — they are about preserving the product's voice
and philosophy.

When an external PR touches any of these three areas, the reviewer
must `AskUserQuestion` before approving.

---

## 1. `ETHOS.md` is off-limits to external rewrites

The ethos is Garry Tan's personal philosophy. Contributors can
propose additions via issue discussion, but PRs that edit
`ETHOS.md` directly are rejected regardless of how reasonable the
proposed text looks.

**Why:** the ethos is *load-bearing*. The three principles (Boil the
Lake, Search Before Building, User Sovereignty — see
**[/product/ethos.md](../product/ethos.md)**) shape every skill.
A well-meaning rewrite that softens them undermines the product's
thesis, even if the new prose reads better as English.

---

## 2. Voice and tone are not open to "professionalization"

Any PR that neutralizes direct prose, strips humor, or converts the
product's CLI-native voice into corporate SaaS tone is rejected.

Specifically:

- "This is wrong because X" rewritten to "You might want to consider
  whether X could potentially be an area for improvement" → **reject**.
- Skill description prose made more "professional" by removing
  contractions, dropping colloquialisms, or adding hedging → **reject**.
- Humor in comments or CHANGELOG entries stripped "for
  consistency" → **reject**.

**Why:** directness + humor are intentional. They are what
differentiates gstack from generic dev tool marketing. See
**[/product/design-system.md](../product/design-system.md)** for the
voice rules.

---

## 3. Promotional material is load-bearing, not decoration

References to Y Combinator, Garry Tan's founder perspective, specific
productivity metrics (810×, 1,237 contributions YTD, etc.), and the
"one person + AI replaces 20-person team" framing are not
interchangeable marketing copy. They are the product's positioning.

PRs that strip these references "to make it more neutral" are
rejected. So are PRs that dilute metric claims ("roughly 800×" for
"810×") or hedge the thesis ("potentially replace" for "replaces").

**Why:** gstack is an opinionated tool with a specific thesis. The
promotional material communicates that thesis. A neutralized README
sells a neutral tool — not gstack.

---

## How reviewers apply this

When a PR touches any of the above categories, before approving:

1. Flag the affected change to the user.
2. `AskUserQuestion`: "This PR changes ETHOS / voice / promotional
   framing. That falls under community-guardrails. Confirm approval?"
3. On no/skip: request changes from the contributor, citing this
   document.

This is deliberately friction for external contributors and
deliberately low-friction for the founder. The balance is the point.

---

## What this is not

- **Not a code-quality gate.** Code-level PRs are reviewed normally.
  Voice rules apply to prose, not to the logic of skills.
- **Not permanent.** The founder can change the ethos or voice at any
  time. External contributors cannot.
- **Not unfriendly.** External contributors are welcome; this document
  exists so they do not waste time on PRs that will be rejected, by
  making the rules visible up front.
