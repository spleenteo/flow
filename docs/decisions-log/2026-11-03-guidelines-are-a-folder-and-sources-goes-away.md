---
kind: decision
status: active
superseded_by: null
work: guidelines-folder
area: workflow
touches: [skills/dev/SKILL.md, shared/work.md, skills/dev/development-guidelines.template.md]
description: "Guidelines are the files of <root>/guidelines/, chosen per slice by description; the sources: key goes away. Rules out a fourth mechanism."
---

# A project's guidelines are the files of the guidelines folder, and sources: goes away

**Date**: 2026-11-03

## The decision

A project's guidelines are the files of `<root>/guidelines/`, each with a `description` and an optional `applies` in its frontmatter, chosen per slice by the conductor; the `sources:` key of `.flow.yml` is removed, with an upgrade case that turns its entries into frontmatter.

## Context

A developer adopting Flow on a Rails app already kept his guidance split by concern (tests, devops, naming) and found one `development-guidelines.md` too long and misleading per slice. Flow had four mechanisms for "what the agents read" (presets, one guidelines file, `sources:`, territories) and the owner could not explain the difference between `sources` and territories without reading the code.

## Alternatives discarded

- **Extend `sources:` with a description and folder paths, keeping the key**: a fourth mechanism grows instead of one disappearing; the declaration stays in configuration, a second list to keep in sync with the files.
- **A per-stack catalog of guidelines reused across projects**: the need shown was per project; recorded as an idea, not built.
- **Descriptions in a central index (in `.flow.yml` or the scheda) instead of frontmatter**: a list to keep in sync; frontmatter travels with the file and lets existing documents be adopted by adding two lines.

## Consequences

Step- and phase-scoped sources (`{step: plan}`, `{phase: impact}`) have no equivalent and are dropped by the upgrade, named. The scheda (`development-guidelines.md`) stays as the structured file for Tests, Gate, References and Deviations and travels whole in every packet; the section-level selection inside it ends.

## Reopen when

A real case needs a document that must reach one role only (the planner alone, the reviewer alone), or a project needs the same guidelines across many repos.

## References

- [brief](../briefs/2026-11-03-guidelines-folder.md)
- [slices](../work/2026-11-03-guidelines-folder/slices.md)

## Status log

- 2026-11-03 — recorded
