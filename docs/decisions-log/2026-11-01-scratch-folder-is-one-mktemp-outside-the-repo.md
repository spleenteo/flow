---
kind: decision
status: active
superseded_by: null
work: doc-territories
area: engine
touches: [shared/territories.md, shared/work.md, skills/dev/SKILL.md]
description: "The session scratch folder is one `mktemp -d` outside the repo, one fresh subfolder per write; it rules out folders in the git dir or the work tree."
---

# The session scratch folder is one mktemp folder outside the repo

**Date**: 2026-11-01

## The decision

The session scratch folder, which holds packets carrying territory text and the documents composed before an adapter write, is one folder made with `mktemp -d` outside the repo, once per session; each write gets a fresh `mktemp -d` subfolder inside it.

## Context

Territory text must reach subagents without connectors and must never be committed or left in the work tree; the folder had to be readable by background subagents in an interactive session. A manual test on 2026-11-01 showed a background subagent reading a file there in default mode with no permission prompt.

## Alternatives discarded

- **A folder inside the repo's git dir**: in a worktree the git dir sits outside the work tree, and writes under `.git` are protected, so they prompt or fail.
- **A gitignored folder in the work tree**: it invites committing territory text and leaves a second copy beside the code.

## Consequences

A resume in a fresh session has no scratch folder and reads again what it needs; the folder is left to the system's temp cleanup.

## Reopen when

Claude Code starts prompting for or denying subagent reads outside the project, or a project needs the scratch content to survive across sessions.

## References

- [spike-scratch-location](../work/2026-10-31-doc-territories/spike-scratch-location.md)

## Status log

- 2026-11-01 — recorded
