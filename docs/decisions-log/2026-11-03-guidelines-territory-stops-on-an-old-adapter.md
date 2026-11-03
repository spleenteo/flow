---
kind: decision
status: active
superseded_by: null
work: guidelines-folder
area: workflow
touches: [shared/territories.md]
description: "A guidelines territory whose adapter answers op: list with unmet stops the run; Flow never degrades to the scheda alone."
---

# When the guidelines territory's adapter cannot list, the run stops instead of degrading to the scheda

**Date**: 2026-11-03

## The decision

When the guidelines kind is on a territory and its adapter answers op: list with unmet, the run stops like on any other unmet; Flow never degrades to the scheda alone.

## Context

The guidelines kind moves from one document to many (a folder with description and applies frontmatter), so the contract gains op: list for guidelines. Existing adapters don't answer it. The contract's rule since v0.5.0 is "never fall back"; the only adapter in existence is maintained by the same team that updates Flow.

## Alternatives discarded

- **Degrade to the scheda alone with one warning line**: the first exception to the no-fallback rule, which every adapter author would then have to learn; it also hides that the project's guidelines are missing from every packet.

## Consequences

A project with a guidelines territory must update its adapter before updating Flow, or its first /flow:dev stops with a message naming the adapter and the contract change. The changelog entry of this work names the change for adapter authors.

## Reopen when

An adapter is maintained by someone who cannot update it in step with Flow, or a second adapter appears.

## References

- docs/briefs/2026-11-03-guidelines-folder.md
- docs/work/2026-11-03-guidelines-folder/slices.md (V3)

## Status log

- 2026-11-03 — recorded
