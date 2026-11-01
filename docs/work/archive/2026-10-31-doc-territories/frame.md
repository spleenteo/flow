---
work: doc-territories
updated: 2026-10-31
tags: [work, doc-territories, frame]
description: "Documentation territories: each kind of project document can live in a place and a tool the project chooses, with docs/ as the default"
---

## The criterion

- **Problem**: Flow reads and writes its documents only at fixed paths under `root:`, so a team whose decisions, product context or guidelines live in another tool (a wiki, a project board) can't point Flow at them, and the files its guidelines cite reach the agents only by chance.
- **Outcome**: a project declares, per kind of document, where it lives, through which adapter and with which writing peculiarities; Flow reads, writes and searches there and brings each agent the sources it needs. With nothing declared, everything stays under `docs/` as today.
- **Less about**: rewriting Flow's steps (review, gate, close); shipping adapters for specific services inside Flow; deciding for a project which document goes where; copying a territory's content into the repo.
- **Measure**: a document or change serves this work if it lets an agent or a teammate reach the right source without a human relaying it, leaves an unconfigured project behaving exactly as before, and puts no token or public share link in a repo.

# Frame — Documentation territories

## Sources

A brainstorm and a grill held in a private project that uses Flow with a team, on 2026-10-31. They are not cited here; what follows is the general part.

## The problem

A project grows across several repos and several tools: code in git, long-lived documents in a wiki, coordination on a project board. Which document goes where is a convention one person knows. Flow can't help, because it knows one place only:

- It reads and writes fixed paths under `root:`: `development-guidelines.md`, `impact-lenses.md`, `decisions-log/`, `CHANGELOG.md`, `work/`. `root:` is the only knob.
- The files a project's guidelines list under `## References` (a testing guide, a pricing model) are not followed by rule: they reach an agent only if the model opens them on its own. Folders like a product description are read by nobody.
- There is no hook to give the shaping and the frame the product's context.
- The changelog's labels (`Work`, `Decision`) and their layout are fixed, and the language is picked by `flow:docs` on its own.
- `changelog-propose` looks for `<root>/work/<slug>/`, without the date the folder name carries.

## What changes

**Territories.** A kind of document (decisions, changelog, guidelines, product context, and the others Flow reads) is a territory. For each one a project can declare where it lives, which adapter serves it and its writing peculiarities. An undeclared territory is served as today, from `docs/`. A territory supports three operations: read, write, search.

**Adapters.** One adapter per tool, chosen on the same pattern as the design adapter (a skill mapped by qualified name). Flow ships the default one, the repo's `docs/`; adapters for other services live in other plugins.

**The conductor carries the content.** Flow's agents have no connectors (only `Read`, `Grep`, `Glob`, `Bash`). The conductor, which has them, reads from the territory through the adapter and puts the excerpt in the agent's packet. The excerpt lives in the session, not in the repo, so it is not a second copy. When a territory can't be reached (connector missing, headless session), Flow stops and says so instead of going on without it.

**Writing outcome.** An adapter's write returns *written* or *proposed, with a link* (for example a comment a human accepts or not). A work's close lists the proposals still pending. Which territory is written in which way is the project's declaration, carried to the adapter; Flow only knows the two outcomes.

**Fields without frontmatter.** `flow:docs` relies on the decisions' frontmatter (`description:`, `touches:`, `work:`, `status:`, `superseded_by:`, …). On a service without frontmatter the convention is a key/value table at the top of the document that stands in for it. The adapter contract says how fields are written and queried.

**Guidelines as a set of sources.** Each source carries when it applies (a testing guide to tasks that touch tests, a pricing model to tasks that touch plans), and the conductor puts it in the packets that match.

**Product context.** A hook that gives the shaping and the frame the product's context when they open.

**Changelog.** Labels, language and the layout of the decision lines become configurable, with Flow's current format as the default. `changelog-propose` finds the work folder with its date.

**Search.** `flow:docs` with `op: search` asks every configured territory.

## Out of scope

- Adapters for specific services: they belong to the plugins that use them.
- Rules on which document a project keeps where.
- Moving existing documents: a project migrates on its own.

## Open questions

- Where the declaration lives: `.flow.yml` keys per territory, or a file the project names. The tokens and per-user values never in a repo.
- How a territory adapter relates to the default: it replaces it for that territory, as a preset replaces the shipped one ([stack presets are replaceable skills](../../../decisions-log/2026-10-26-stack-presets-are-replaceable-skills.md)), or it can fall back to it.
- Which kinds of document are territories: `work/` and `.version` probably stay in the repo, since they change with the code.
