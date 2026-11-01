---
kind: decision
status: active
superseded_by: null
work: doc-territories
area: workflow
touches: [shared/work.md, shared/territories.md, skills/archive/SKILL.md, skills/dev/SKILL.md]
description: "A proposed archive stops the close before the merge until the proposal is accepted; merging with the work folder still in the repo is ruled out."
---

# A proposed archive stops the close before the merge

**Date**: 2026-11-01

## The decision

When the archive on a territory returns `proposed <link>`, the work's close stops before the merge: nothing is removed, the pending line goes in `STATUS.md`, and the close resumes once the proposal is accepted and `/flow:archive` is run again (its report offers Step 6 move 4).

## Context

With an archive territory whose write word proposes instead of writing, the folder cannot leave the repo until a human accepts the proposal; the close had to choose between waiting and merging with the folder still in place.

## Alternatives discarded

- **Merge with the work folder still in the repo and archive later**: main would carry a work folder that an accepted proposal later removes, and the archive would become a separate change after the release.

## Consequences

A close can stay open for as long as the proposal waits; the pending proposals list in STATUS.md is what shows it.

## Reopen when

Teams find closes blocked for long on archive proposals, or an adapter can confirm acceptance by itself.

## References

- [slices](../work/2026-10-31-doc-territories/slices.md)

## Status log

- 2026-11-01 — recorded
