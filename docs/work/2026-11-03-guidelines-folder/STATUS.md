---
status: open
phase: slice
slice: V4
step: execute
work: guidelines-folder
stacks: [generic]
base: main
updated: 2026-11-04
tags: [work, guidelines-folder]
description: "A project's guidelines become a folder docs/guidelines/ of files with description and applies frontmatter, chosen per slice and named at the first stop; sources: is removed with an upgrade case; the guidelines territory becomes multi-document with no fallback; Flow's own repo adopts the folder first."
---

# Status — Guidelines folder

> Update at the **end of every session**: done, remaining, blockers.

**Entry**: technical, decided on 2026-11-03 because the outcome fits one sentence: a project puts its guideline documents in `docs/guidelines/` with a description, and each slice receives only the ones that concern it. Brainstorm and builder check done by hand before opening: `docs/briefs/2026-11-03-guidelines-folder.md`, Work 1.

## Slices

- [x] V1 — The folder in the conductor (2026-11-04)
- [x] V2 — `sources:` goes away, with its upgrade (2026-11-04)
- [x] V3 — Guidelines on a territory, many documents (2026-11-04)
- [ ] V4 — This repo adopts the folder; the docs show it

## Pending proposals

<!-- date — kind:name — link, when an adapter returns proposed -->

## Log

<!-- date — step — done — remaining — wall-clock — cost -->
- 11-03 — opening — brief approved, home created on work/guidelines-folder — slicing next — — —
- 11-03 — slicing — 4 slices in slices.md; fidelity check skipped (no frame) — impact next — — —
- 11-03 — impact — 23 findings (4 high), all folded into slices.md; no split or reorder; 2 decisions recorded — plan V1 next — — —
- 11-04 — V1 close — 7 commits, gate green (link check narrowed to skip this work's packets; V4 makes it permanent), 5 lessons, no decision — V2 plan next — ~17 min runner — —
- 11-04 — V2 close — 13 commits, gate green, final review done, pending --plugin-dir upgrade run passed headless, 5 lessons, no decision — V3 plan next — ~48 min runner — —
- 11-04 — V3 close — 6 commits (2 fix rounds, same applies: null gap), gate green, final review done (lows only), 4 lessons, no decision — V4 plan next — ~19 min runner — —
