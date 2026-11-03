# Guidelines as a folder, then documentation with one role per page

Two works, in this order. The first changes what a project tells Flow about how its code is written; the second rewrites the documentation around the result. Written from a `/flow:brainstorm` on 2026-11-03.

## Why

Flow was installed by a developer outside this repo, on a Rails application. He already keeps his guidance split by concern: a file for the test suite, one for devops (Fly, Cloudflare), one for naming conventions. Flow asked him for one `docs/development-guidelines.md`. One file is too long to read, and it puts rules in front of the agents that have nothing to do with the work at hand: a deploy job doesn't need the testing rules; a feature doesn't need the Fly layout.

He found no way to do it. The README says a stack's rules live in a preset (one `SKILL.md`, in the plugin); his own rules live in one guidelines file; `sources:` adds files one by one with a fixed rule; `territories:` says where a kind of document lives. Four mechanisms for "what the agents read", described in a 4,000-word README, and the owner of this repo could not explain the difference between `sources` and `territories` without reading the code. The owner's words: *"faccio molta fatica a capire la differenza e lo scopo"*.

Both facts point at the same thing. The model has one concept too many, and the documentation describes mechanisms instead of a model.

## Work 1: `docs/guidelines/`

### Intent

A project's guidelines are a folder of files the project already has, each saying what it is for. Flow picks the ones a slice needs and says which it picked. Nothing to declare for the common case.

### What changes

**The folder.** `<root>/guidelines/` holds the project's guideline documents, any number, Markdown, any names, sub-folders free (a project with several stacks may use `guidelines/rails/`, `guidelines/astro/`; Flow gives no meaning to the sub-folder). Each file opens with a frontmatter of two keys:

```markdown
---
description: Rules for the test suite, TDD, feature tests with Playwright
applies: ["spec/**", "test/**"]
---
```

- `description` (required): one line, what the document is for and when it matters. The agent reads it to decide. A file without a description is skipped and named in the next message, never read.
- `applies` (optional): a list of globs in the syntax `sources:` uses today (quoted, `*` within a segment, `**` across), or the word `always`. Absent, the document is chosen by its description.

**The scheda stays.** `<root>/development-guidelines.md` keeps its role as the structured file Flow reads for `## Tests` (level, sensitive areas), `## Gate`, `## References` and `## Deviations`. The profile's draft and the template stay; the draft writes those four sections only, no longer "the code's observed conventions" (that is folder material). The scheda goes into the packet **whole**, always: today's selection of "the sections that apply" inside it ends, since selection now happens between files. What leaves it is the long prose about naming, placement and tests: that prose becomes files in the folder. The template says so at the top. An existing scheda with sections beyond the four keeps working; the upgrade names those sections as candidates to move into the folder, and edits nothing.

**Selection.** When the slice packet is built (and at impact, with the slices' paths):

1. A file with `applies: always` is in.
2. A file with globs is in when the slice's paths match them (the rule `sources:` uses: the slice's section of `slices.md`, then the planner's `Files:` after the plan, with the late match appended before the review).
3. A file with no `applies` is in when the conductor judges its description relevant to the slice's section of `slices.md`. The judgment is the conductor's, from the descriptions alone, the way it reads the `description:` line of decisions today; it never opens a file to decide.

The packet carries the chosen files under `## Guidelines`, one `### <path>` per file, text verbatim, after the scheda's sections. The first stop names the chosen files, with their line counts, in one line, and the files left out are not named (there may be many). A wrong choice is corrected by rewriting a description or adding `applies`, never by configuration.

**Moments without slice paths.** The standing documents at opening and resume, the criterion block handed to `shaping` and `framing-doc`, and the fidelity check's packet get the scheda plus the `applies: always` files. Judgment by description happens only where there are slice paths: the slice packet and impact.

**First run.** Step 0's message gains one sentence naming `<root>/guidelines/` and its two frontmatter keys, so a developer with existing split documents knows where to put them. No detection of guidance elsewhere in the repo, no offer to move files: that would turn the first run into a migration.

**`sources:` goes away.** Everything it did is covered: a path-scoped source is a guideline file with `applies`; a step- or phase-scoped source has no equivalent and is dropped (see Non-goals). The key is removed from `.flow.yml`'s documented keys, from `skills/dev/SKILL.md` § Sources, from `shared/work.md`'s dispatch contract and from `shared/territories.md`'s rows about sources naming a territory.

**Upgrade.** The existing upgrade (`shared/work.md` § Upgrade) gains one case. A `sources:` key on the default branch is an old trace. An entry that keeps at least one glob becomes two frontmatter lines at the top of its file (`applies` with the globs, a `description` drafted from the file's first heading and marked for the user to rewrite), and the file is moved under `<root>/guidelines/` with `git mv` when it isn't there already; entries sharing a path merge their globs into one `applies`. An entry with no glob left (only `{step}` or `{phase}` values) is dropped and named, and its file stays where it is: moving it would let judgment pick it for any slice, a wider reach than its owner chose. A `{territory, name}` entry is dropped and named too (the kind now lists its own documents). Inbound references to a moved file (`@docs/...` imports in `CLAUDE.md` files, Markdown links) are pointed at, not edited, as the existing upgrade does for `CLAUDE.md` lines. Sections of the scheda beyond the four are named as candidates for the folder. The key is removed from `.flow.yml`. Same branch, same commit, same ok as the rest of the upgrade.

**Guidelines on a territory.** Today `guidelines` is a single-document kind in `shared/territories.md` (`op: read` with no `name`). It becomes a multi-document kind like `decisions`: `op: list` returns one line per document with its name, description and `applies` (`applies` joins the contract's § Fields), `op: read; name: <name>` returns one, and `op: read` with no `name` keeps returning the scheda, so that read is unchanged. `where` keeps meaning what the project declared; how the scheda and its sibling documents sit there is the adapter's business, and the contract says so. The `## Tests` write keeps targeting the scheda. Selection on a territory works from the `list` lines exactly as from the frontmatter in the repo. The adapters are not Flow's; this repo updates the contract, and names the change in the changelog so adapter authors see it. An adapter that answers `op: list` for `guidelines` with `unmet` stops the run like any other unmet (§ Resolution), with a message naming the adapter and the contract change: the rule "never fall back" stays whole. See the owner's question below.

**Flow's own repo** adopts the folder first: its `docs/development-guidelines.md` is split, the prose moving into `docs/guidelines/` files with descriptions. This is the test of the model before anyone else uses it.

**Lenses** stay a single file, `impact-lenses.md`. The template's comments gain two examples of a lens, so the file stops looking like a second set of rules: *"the project is multi-tenant: ids repeat across tenants; does the change assume them unique?"* and *"a sandbox environment on DatoCMS costs money: does the change create one without warning?"*. The README's one-line distinction (rules go in the guidelines, what a change can break goes here) stays.

### Goals

- A developer with existing split documentation adopts Flow by adding two frontmatter lines per file and moving the files into one folder.
- The agents of a slice receive only the guidelines that concern it, plus the scheda, and the user sees which.
- `.flow.yml` loses a key and gains none.
- The same folder works on Outline (or any adapter) through the `guidelines` territory, with no change to how a project declares it.

### Non-goals

- Replacing or splitting the shipped presets. They stay one `SKILL.md` each, in the plugin; a project that disagrees maps its own (`presets:`), as today.
- A per-stack catalog of guidelines reused across projects. Everything here is per project. The idea is recorded elsewhere for later.
- A guidelines folder half in the repo and half on a territory. One place per kind, as for every kind today.
- Step- and phase-scoped sources (`{step: plan}`, `{phase: impact}`). Nobody has asked for them since they shipped; a guideline that must reach the planner alone is not a guideline. If a real case shows up, it comes back as its own work.
- Merging `impact-lenses.md` into the folder.

### Anti-goals

- A second list to keep in sync: no index of the folder's files in `.flow.yml`, in the scheda or anywhere else. The frontmatter is the only declaration.
- Flow opening files to decide whether to include them. Descriptions and globs, nothing else.
- Silent inclusion. Every chosen file is named at the first stop.

### Constraints

- Flow is a plugin of Markdown skills: the change is text in `skills/dev/SKILL.md`, `shared/work.md`, `shared/territories.md`, `agents/slice-runner.md` (its `Execute sources:` and `Close sources:` fields go), the templates, `docs/architecture.md`, the README's section on customizing, and the changelog. No code.
- The folder sits under `root:` like every other kind, and moves with it.
- A merge into `main` is a release; the work follows this repo's `development-guidelines.md` § Releases.
- The territories contract is read by adapter authors outside this repo: the `guidelines` change is additive where it can be (`op: read` with no `name` may keep returning the scheda), and the changelog entry states what an adapter must add.

### Decided by the owner

- Guidelines are many files in a folder, chosen by the agent from a description, in the repo or on a territory.
- Descriptions live with the documents (frontmatter), not in configuration.
- Per project, not per stack.
- `sources:` is removed rather than extended.

### Calls made in this document (the owner can overturn them)

- `applies: always` as the way to force a file in.
- The scheda keeps its name and role, rather than becoming `guidelines/README.md`: no upgrade churn for the profile and the territory write.
- Step and phase sources are dropped, not migrated.
- Files left out are not listed at the first stop, only the ones chosen.
- The upgrade drafts the description from the file's first heading rather than leaving it empty, so the file is valid at once.
- The whole scheda travels in every packet; the section-level selection inside it ends.
- Moments without slice paths (opening, resume, shaping, framing, fidelity check) get the scheda and the `always` files only.
- Step 0 names the folder in one sentence, with no detection or migration of existing documents.
- A `sources:` file with no glob left stays where it is; inbound references to a moved file are pointed at, not rewritten; globs of entries sharing a path merge.
- On a territory, `where` and `op: read` without `name` are unchanged, and an old adapter's `unmet` on `list` stops the run as any unmet does.

### Left to the builder

- The exact wording of the packet's `## Guidelines` section and the first stop's line.
- Where in `skills/dev/SKILL.md` the selection rule sits (§ Sources is replaced; § Project memory and the slice packet paragraph reference it).
- How the upgrade's message lists the dropped entries.
- Whether `install.sh` needs to know the folder (it reads `.flow.yml` for stacks only; probably not).
- The `list` line format for `guidelines` in the contract (today's is built for decisions).

## Work 2: documentation with one role per page

### Intent

Someone who has never seen Flow reads the README in ten minutes and runs a first work; someone customizing it finds one page that answers "where do I put this"; someone developing the plugin finds the diagrams and the tables, and reads the skills for the rest.

### What changes

Four documents, one role each, no sentence in two of them:

| Document | Role | Holds |
|---|---|---|
| `README.md` | Start | What Flow is (one paragraph), install, the first work in a project, one example of customizing (the guidelines folder), links to the three others. Target: under 1,200 words |
| `docs/configuration.md` (new) | Reference | Every key of `.flow.yml` with its values and defaults; `territories:` and the adapter contract's user-facing part; plugin options and environment variables; the upgrade from older names. Moved out of the README's Configuration, Upgrading and Profile sections |
| `docs/architecture.md` | Map | The four diagrams, the "written by / read by" table, the path variables, the trust-dialog note. Under each diagram one line per node with a link to the skill section, in place of today's narrative bullets. Target: about 100 lines, from 250 |
| The skills | Truth | Unchanged by this work |

Cut from `architecture.md`: the install commands and the `settings.json` snippet (README), the eight lifecycle bullets (the skills), the decision-file paragraph (`skills/docs/decisions.md`), Project memory (the skill), the dependencies prose (`dependencies.md`). Cut from the README: How a work runs beyond one paragraph, Project lenses and Plan review lenses (one line each, linking the skill), Configuration, Upgrading, Develop the plugin (a link to `development-guidelines.md`).

The site (`site/index.html`) follows the README's new shape; its content is the README's, not a fifth document.

### Goals

- A reader can say, after the README alone, what a preset, a guideline, a lens and a territory are, in one sentence each.
- No fact is stated in two documents. A detail that changes is edited in one place.

### Non-goals

- Changing any behaviour. If the rewrite finds a contradiction between a document and a skill, the skill wins and the document is corrected; the skill is not touched.
- Translating the documentation.

### Decided by the owner

- The README is too long and people who tried it struggled; it is rewritten, not trimmed.
- `architecture.md` is kept for its diagrams and tables, and loses its narrative.

### Calls made in this document

- One new page, `docs/configuration.md`, rather than several.
- The site mirrors the README rather than keeping its own text.
- The 1,200-word and 100-line targets, as a ceiling to argue against, not a rule.

### Left to the builder

- The order of the README's sections and the exact split of the Configuration prose between the README's example and the reference page.
- Whether `docs/briefs/` and `docs/decisions-log/` get a mention in `architecture.md`'s table (they are not Flow's outputs, they are this repo's).

## Order and size

Two works, Work 1 first: the README's example of customizing must describe the folder, and `configuration.md` must not document `sources:`. Each is a project in Flow's terms: opened with `/flow:dev`, with this document as its brainstorm. Work 1 is the larger: it touches the contract and the upgrade, and its own repo is its first user.
