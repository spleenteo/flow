---
kind: decision
status: active
superseded_by: null
work: doc-territories
area: engine
touches: [skills/docs/SKILL.md, skills/dev/SKILL.md, shared/work.md]
description: "Territory adapters run in the conductor's main session; flow:docs stays file-based. Rules out adapters behind flow:docs and a local mirror."
---

# Territory adapters are invoked by the conductor in the main session

**Date**: 2026-10-31

## The decision

Territory adapters are invoked by the conductor in the main session; flow:docs keeps working on repo files and on excerpts the conductor hands it, and writes to external territories go through the adapter from the conductor.

## Context

Documentation territories let a project keep decisions, changelog, guidelines, impact lenses, product context and the archive in another tool through an adapter skill. Flow's agents have no connectors; the main session does. A place had to be chosen for the adapter calls.

## Alternatives discarded

- **Adapters behind flow:docs, invoked from its fork**: lost because it is unverified that a forked skill can invoke another skill and reach MCP connectors, so reads, writes and the decision fields would rest on a guess.
- **A local mirror synced into a gitignored folder**: lost because it is a second copy that goes stale between syncs, invites being committed, and a stale mirror is a territory Flow believes it reached.

## Consequences

The conductor grows a resolve-and-fetch step before packets and flow:docs requests that touch an external territory; flow:docs stays file-based and unchanged for unconfigured projects; an unreachable territory stops the conductor, not a subagent.

## Reopen when

It is verified that a forked skill or subagent reliably reaches the session's MCP connectors and can invoke another plugin's skill.

## References

- [shaping](../work/2026-10-31-doc-territories/shaping.md)

## Status log

- 2026-10-31 — recorded
