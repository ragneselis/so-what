# discovery-kit - Contributor & Maintainer Guide

This file is for people developing the discovery-kit plugin. **It is not loaded at
runtime.** Claude Code only auto-loads `CLAUDE.md` from a user's own
project/home tree, never from an installed plugin's directory - so nothing here
changes how the plugin behaves for users. Every agent-facing rule must live in
the skills (see below).

discovery-kit teaches product builders product management by forcing structured
discovery before building: a hub skill plus six discovery skills that
interrogate the user through opinionated multiple-choice questions and write
artifacts into `product/`.

## Architecture

- `skills/discovery-kit/` - root hub. Shows progress across artifacts, recommends
  the next step, routes to the other skills, and enforces the implementation
  guard. Invoked first, and re-invoked after each sub-skill completes.
- Six discovery skills, run in this order, each writing one artifact to `product/`:

  | Skill | Artifact |
  |-------|----------|
  | `problem-statement` | `product/problem-statement.md` |
  | `interview-script` | `product/interview-script.md` |
  | `competitive-research` | `product/competitive-research.md` |
  | `jtbd` | `product/jtbd.md` |
  | `personas` | `product/personas.md` |
  | `product-doc` | `product/PRODUCT.md` (final; hands off to implementation) |

- A shared risk register, `product/open-questions.md`, is written and read by
  several skills rather than owned by one. Discovery skills append unknowns to it,
  the hub surfaces it, and `product-doc` gates on it.
- There is one interview step: `interview-script`, run early (step 2) to validate
  the problem JTBD-style. `jtbd` does not generate interviews; it drafts
  job hypotheses and synthesizes the interview results.
- Interview notes/transcripts are an any-time input in `product/interviews/` (one
  file per interview, or pasted into chat and persisted there). `jtbd` is
  the single synthesis engine: it processes only files whose first line lacks
  `discovery-kit: synthesized`, then stamps each handled file with that marker. The hub
  counts new vs handled interviews and nudges the user to interview (when testable
  questions are open) and to synthesize (when new interviews arrive).
- `skills/feature/` - a parallel mode for existing products. Once `product/PRODUCT.md`
  exists, a new feature goes through `discovery-kit:feature` (scoped discovery from the
  feature's own problem + register the riskiest assumption) and lands in PRODUCT.md's
  `## Features` backlog as `Status: Open`, flipped to `Status: Done` when built. The
  greenfield six are untouched; the hub routes to it when a product already exists.

## Where runtime behavior lives (and why not here)

Because this file never reaches the model, every agent-facing rule lives inside
the `SKILL.md` files:

- **Invocation / detection** - the `description` frontmatter of
  `skills/discovery-kit/SKILL.md`. That frontmatter is the only thing in the system
  prompt that can trigger the plugin; there is no other detection mechanism.
- **Personality / how to ask questions** - a `## Personality` block inlined in
  each of the six discovery skills and the `feature` skill. The block is identical
  across all seven and is the **single source of truth**. If you change a rule,
  change it in all seven.
  The most important rule: the `AskUserQuestion` `question` field must teach, not
  just ask - never a bare label like "WHO".
- **Open-questions register protocol** - a second inlined block (`## Open
  Questions: register, don't dump`) telling a skill how to append unknowns to
  `product/open-questions.md`. It is mirrored across `problem-statement`,
  `competitive-research`, `jtbd`, `personas`, `interview-script`, and
  `feature`.
  Like the Personality block, it is duplicated on purpose - change one copy, change
  them all. The disposition gate over the register lives in
  `skills/product-doc/SKILL.md`; the surfacing/routing of open questions lives in
  `skills/discovery-kit/SKILL.md`. The register also carries a `revise -> <artifact>` item type: when new evidence contradicts an upstream artifact, a skill logs a revise item naming it, the hub surfaces it first and routes back to that skill, and the gate forces a decision. This is discovery-kit's lightweight backward-propagation - no per-artifact status lines.
- **Implementation guard** (block building until `problem-statement.md` and
  `PRODUCT.md` exist) - `skills/discovery-kit/SKILL.md`.
- **Build-agnostic handoff / Superpowers coexistence** - `skills/product-doc/SKILL.md`
  treats the post-discovery handoff as build-agnostic by default (code, no-code,
  prototype, or a brief for someone else); only when the user is building with code
  does it optionally hand off to `superpowers:writing-plans`, if installed.
  discovery-kit skills never invoke Superpowers skills otherwise, and never touch
  implementation, debugging, code review, or testing.

## Conventions

- Each artifact opens with `# Title` and `> Generated by discovery-kit - [date]`,
  and closes with a Reasoning Trail and a one-line pointer to the shared
  open-questions register (`product/open-questions.md`). Unknowns are appended to
  that register, never written as a per-artifact prose Open Questions section.
- One question at a time, always via the `AskUserQuestion` tool. No compliments,
  no validation - silence is approval.
- Use the user's own words in artifacts (especially differentiation claims); do
  not polish them.

## Release process

1. Edit skills. If you touched a `## Personality` block or the `## Open Questions:
   register, don't dump` block, mirror the change across every skill that carries
   it (Personality: the six discovery skills plus `feature`; register protocol: the
   five listed above plus `feature`).
2. Bump `version` in **both** `.claude-plugin/plugin.json` and
   `.claude-plugin/marketplace.json`, keeping them equal.
3. Commit and push to `master`. Plugin updates propagate by version number, so a
   bump is required for the change to reach anyone. Users update with `/plugin`.
