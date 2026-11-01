---
name: archive
description: Use to archive a finished work: a `/flow:dev` work already recorded in the changelog, or a closed `/flow:project` home. Triggers include "/flow:archive", "archive the work X", "archive the project X", "put X away". Never archives on its own initiative.
---

# Flow: archive

Moves a finished work or project home out of the way and repairs what pointed at it. Nothing is lost: the folder moves to `<root>/work/archive/`, or to the home's sibling `archive/` for a project; on an `archive` territory it goes to its copy there, and the repo's git history keeps the removed folder. Invoked on its own or from the closing sequence of `/flow:dev` or `/flow:project`, always after the user confirms.

## Layout

When an older configuration file is present (the work reference, § Upgrade, names it), even beside `.flow.yml`, stop and point to `/flow:dev`, which upgrades it.

- A repo with `<root>/work/` (default root `docs`): repo layout.
- A project home whose `STATUS.md` carries `project:`: project layout.

If neither fits, ask the user which it is.

## Procedure

Follow `${CLAUDE_PLUGIN_ROOT}/shared/work.md` (in the repo: [shared/work.md](../../shared/work.md)) § Archive for the chosen layout: Archive, repo or Archive, project. When `territories:` is declared in `.flow.yml` (any kind), read the contract (`${CLAUDE_PLUGIN_ROOT}/shared/territories.md`) first. With `archive` declared (repo layout only), follow § Archive, repo, on a territory (`#archive-repo-on-a-territory`); on repo layout without `archive`, follow Archive, repo; its step 2 goes through the adapters for a declared `changelog` or `decisions`.

Report as that section says.
