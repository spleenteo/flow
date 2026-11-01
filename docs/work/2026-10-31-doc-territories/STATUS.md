---
status: open
phase: slice
slice: V8
step: execute
work: doc-territories
stacks: [generic]
base: main
updated: 2026-11-01
tags: [work, doc-territories]
description: "Documentation territories: each kind of project document can live in a place and a tool the project chooses, with its writing peculiarities; with nothing declared, everything stays in docs/"
---

# Status — Documentation territories

> Update at the **end of every session**: done, remaining, blockers.

**Entry**: product, decided on 2026-10-31 because the outcome is clear but the solutions differ (adapter contract, relation to the default); the problem was already defined in an external brainstorm and grill, so the frame is written from it.

## Slices

- [x] V1 — the changelog adapts to the project
- [x] V2 — guidelines as a set of sources
- [x] V3 — the adapter contract and its resolution
- [x] V4 — reads through territories
- [x] V5 — writes through territories
- [x] V6 — the archive on a territory
- [x] V7 — cleanup from V6's final review
- [ ] V8 — a leaner conductor, and the audit's lows

## Log

<!-- date — step — done — remaining — wall-clock — cost -->
- 2026-10-31 — frame — home opened, frame written from external source material — shaping — ~15 min — —
- 2026-10-31 — shaping — shape A selected (conductor-side adapters); fidelity check: 8 findings, all applied — slicing (breadboard skipped: no new places, changes are to existing skills) — ~50 min — —
- 2026-10-31 — slicing — 6 slices; fidelity check: 10 findings (Done lines), all applied; R5 allows native fields (data attributes, custom fields) besides the key/value table — impact — ~25 min — —
- 2026-10-31 — impact — two agents: 24 findings, 20 after merging (4 high: committed packet with external excerpts, archive links and slug check, profile on a guidelines territory, step-scoped sources vs packet cache); all applied to slices.md — V1 plan — ~15 min — —
- 2026-10-31 — V1 close — 4 tasks, 4 commits, all passed first review; gate green; final review: 4 low; 4 lessons — V2 plan — ~25 min — —
- 2026-10-31 — V2 close — 3 tasks + T4 check (BLOCKED by the safety classifier on printed prompts, redone as a routing table); final review: untracked-file leak fixed; gate green; 3 lessons — V3 plan — ~35 min — —
- 2026-10-31 — V3 close — contract shared/territories.md, resolution before the profile; headless runs: adapter called by qualified name, stops name the territory, no-key baseline identical; read order carried to V4; gate green; 3 lessons — V4 plan — ~30 min — —
- 2026-10-31 — V4 close — reads through territories (§ Reads, excerpts:, read order held after fix, scratch packets); 8 tasks, 17 fix commits, final review 3 fixes; gate green; 6 lessons; carried to V5: product-cite rule in tool prompts, spike on scratch location — V5 plan — ~60 min — —
- 2026-11-01 — V5 close — writes through territories (§ Writes, scratch composition, pending proposals, grill and changelog via adapter); spike: mktemp -d kept, git dir ruled out; final review 4 fixes; gate green — V6 plan; pending for the close: interactive default-mode check of a background Read in mktemp -d; decision candidate on the scratch place — ~60 min — —
- 2026-11-01 — V6 close — archive on a territory (op: archive, guarded git rm, inbound links as plain text, slug check and search on the archive territory); runner died on an expired login and resumed from the ledger; gate green; follow-ups logged — work close — ~70 min — —
- 2026-11-01 — audit — Fable read of the whole change: 26 findings (3 high, 11 medium, 12 low), report in audit-fable.md; V7 widened to the highs and mediums except M6; V8 added for M6 and the lows — V7 plan revision — ~15 min — —
- 2026-11-01 — V7 close — audit highs and mediums fixed (version committed after the adapter write, pending archive line restored, closed-work route and merge offer, grill hands results back, runner stops on unreadable packet); gate green; lows carried to V8 — V8 plan — ~45 min — —
