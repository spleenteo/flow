---
shaping: true
work: doc-territories
updated: 2026-10-31
tags: [work, doc-territories, shaping]
description: "Shaping of documentation territories: requirements, CURRENT, shapes and fit check"
---

# Documentation territories — Shaping

Frame: [frame.md](frame.md). User decisions taken before shaping: the declaration lives entirely in `.flow.yml`; `work/` stays in the repo; the archive may go to an external territory.

## Requirements (R)

| ID | Requirement | Status | Serves |
|----|-------------|--------|--------|
| **R0** | **A project declares, per kind of document, where it lives** | Core goal | Outcome |
| R0.1 | The kinds a project can declare are the documents Flow reads or writes outside a work: decisions, changelog, guidelines, impact lenses, product context, archive. `work/` and the version file are outside this boundary and stay in the repo (user decision, 2026-10-31) | Must-have | Outcome |
| R0.2 | Each declaration names the place, the adapter that serves it and its writing peculiarities, in `.flow.yml` | Must-have | Outcome |
| R0.3 | Read, write and search all work on a declared territory | Must-have | Outcome |
| R1 | A project with nothing declared behaves exactly as today, under `root:` | Must-have | Outcome, Measure |
| R2 | Flow itself writes no token or secret into a repo, and its documentation says where tokens and per-user values belong (outside the repo) and that `where` values and links must not be public share links; keeping to it is the user's responsibility (user ruling, 2026-10-31) | Must-have | Measure |
| R3 | Flow's agents, which have no connectors, receive a territory's content in their packet; when a territory can't be reached, Flow stops and says which one | Must-have | Problem, Measure |
| R4 | A write returns *written* or *proposed, with a link*; a work's close lists the proposals still pending | Must-have | Outcome (writing peculiarities) |
| R5 | A decision's fields (`kind`, `status`, `description`, `touches`, `work`, `area`, `superseded_by`, `resolved_by`) are written and queried on a service without frontmatter, through a key/value table at the top of the document | Must-have | Outcome (read, write, search) |
| R6 | The guidelines are a set of sources, each with when it applies; the conductor puts in a packet only the sources that match it | Must-have | Problem, Measure |
| R7 | The product context reaches the shaping and the frame when they open | Must-have | Problem, Measure |
| **R8** | **The changelog adapts to the project** | Must-have | Outcome (writing peculiarities) |
| R8.1 | Labels, language and the layout of the decision lines are configurable; Flow's current format is the default | Must-have | Outcome (writing peculiarities) |
| R8.2 | `changelog-propose` finds the work folder with its date (`<root>/work/YYYY-MM-DD-<slug>/`) | Must-have | Outcome: a changelog on any territory still reads the work's folder, and today it misses it |
| R9 | Flow ships no adapter for a specific service: the contract is public and another plugin implements it | Must-have | Less about |

## CURRENT

| Part | Mechanism |
|------|-----------|
| CUR1 | `.flow.yml` keys: `root`, `changelog` (a path), `version_file`, `areas`, `presets`, `design`, `stacks`, `merge` |
| CUR2 | `flow:docs` is a forked `general-purpose` skill: changelog ops, `search`, `descriptions`, decision write/status/check, all with `rg` and files under `<root>/` |
| CUR3 | Decisions: one file each in `<root>/decisions-log/`, YAML frontmatter, `scripts/decisions-check` validates them; `files` matching reads `touches:` |
| CUR4 | The conductor reads `development-guidelines.md` and `impact-lenses.md` from `<root>/` and puts their sections in packets; `## References` in the guidelines is never followed |
| CUR5 | Adapter precedent: `/flow:design` resolves `design=<skill>` from `presets:`, then `stack_presets`, then a shipped list; the adapter is a skill with a fixed set of sections (`## Requires`, `## Files`, `## Operations`) |
| CUR6 | Archive: `/flow:archive` moves `<root>/work/<dir>/` to `<root>/work/archive/`, walled by a `deny` in `.claude/settings.json`, indexed in `archived.md` |
| CUR7 | Changelog entry: fixed English labels `Work`, `Decision`, one decision per line; language follows existing entries |

## A: Conductor-side adapters, `flow:docs` stays on files

The conductor (main session, with connectors) calls the territory's adapter skill; `flow:docs` keeps working on files and on excerpts the conductor hands it.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| A1 | `.flow.yml` gains `territories:`, one entry per kind: `adapter` (qualified skill name), `where` (the place, in the adapter's terms), `write` (a word the adapter interprets, e.g. `direct`, `comment`). Absent kind → the repo adapter on `<root>/` | |
| A2 | Adapter contract (a doc in `shared/`): sections `## Requires`, `## Fields` (how frontmatter keys map to the key/value table), `## Operations` with `read`, `write` (returns `written` or `proposed <link>`), `search`, `list` (documents with their fields), `archive` (a folder of files, kept as a folder) | |
| A3 | The conductor resolves the adapter at the start of a step that needs a territory, runs `## Requires`, stops on failure naming the territory | |
| A4 | For external territories the conductor fetches through the adapter every read that ends in a packet or a request: what `flow:docs` would have read (descriptions, matches), the guidelines, the impact lenses, the sources, the product context; `flow:docs` writes nothing there — writes go through the adapter from the conductor | |
| A5 | Pending proposals: the conductor appends `- <kind> — <title> — <link>` under `## Pending proposals` in `STATUS.md`; the close adds one line listing them to its report, and its sequence is unchanged | |
| A6 | Sources: `.flow.yml` gains `sources:`, each entry a document (a path, or a territory and a name) with `when` (paths it applies to, or a step: `shaping`, `frame`, `plan`, …); the conductor puts in a packet the sources whose `when` matches the slice's files or the step. `## References` in the guidelines stays prose for humans | |
| A7 | Product context: territory `product`; read at the opening of shaping and frame, its excerpt placed above the path instruction. Undeclared → nothing is read, no default path | |
| A8 | Changelog: `changelog:` accepts a map (`path`, `labels`, `language`, `decisions: lines | inline`); a string keeps today's meaning. Fix of the dated folder in `changelog-propose` | |
| A9 | Archive territory: the adapter's `archive` op writes the work's folder as it is, one container with one document per file (on a service without folders, a parent document with its children); the local folder is removed and no index row is written, since the commits and the changelog record the work, and `search` on the archive territory finds it (user ruling, 2026-10-31). With no archive territory, `/flow:archive` works as today, `archived.md` included | |
| A10 | Documentation: the adapter contract and the README say that tokens and per-user values live in the adapter plugin's own user configuration, never in `.flow.yml` or a repo file, and that `where` values and returned links must be identifiers or links that need a login, never public share links. Flow does not enforce it | |

## B: Adapters behind `flow:docs`

Every territory op, file or external, goes through `flow:docs`, which resolves the adapter and calls it from its fork.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| B1 | Same `territories:` declaration as A1 | |
| B2 | Same contract as A2 | |
| B3 | `flow:docs` reads `territories:`, invokes the adapter skill from inside its fork for every op on a non-repo territory | ⚠️ |
| B4 | `decisions-check` runs on fields the adapter returns, not on files | ⚠️ |
| B5–B9 | As A5–A9 | |

## C: Local mirror

The adapter syncs a territory into a gitignored folder under `<root>/`; Flow keeps reading files.

| Part | Mechanism | Flag |
|------|-----------|:----:|
| C1 | Same declaration as A1, plus `mirror: <path>` | |
| C2 | `sync` op at the start of each session and before each read; writes go back through the adapter | |
| C3–C8 | As A5–A8 | |

## Fit check

| Req | Requirement | Status | A | B | C |
|-----|-------------|--------|---|---|---|
| R0 | A project declares, per kind of document, where it lives | Core goal | ✅ | ✅ | ✅ |
| R0.1 | The kinds are the documents Flow reads or writes outside a work: decisions, changelog, guidelines, impact lenses, product context, archive. `work/` and the version file stay in the repo | Must-have | ✅ | ✅ | ❌ |
| R0.2 | Each declaration names the place, the adapter that serves it and its writing peculiarities, in `.flow.yml` | Must-have | ✅ | ✅ | ✅ |
| R0.3 | Read, write and search all work on a declared territory | Must-have | ✅ | ❌ | ✅ |
| R1 | A project with nothing declared behaves exactly as today, under `root:` | Must-have | ✅ | ✅ | ✅ |
| R2 | Flow itself writes no token or secret into a repo, and its documentation says where tokens and per-user values belong and that `where` values and links must not be public share links | Must-have | ✅ | ✅ | ❌ |
| R3 | Flow's agents, which have no connectors, receive a territory's content in their packet; when a territory can't be reached, Flow stops and says which one | Must-have | ✅ | ❌ | ❌ |
| R4 | A write returns *written* or *proposed, with a link*; a work's close lists the proposals still pending | Must-have | ✅ | ✅ | ✅ |
| R5 | A decision's fields are written and queried on a service without frontmatter, through a key/value table at the top of the document | Must-have | ✅ | ❌ | ✅ |
| R6 | The guidelines are a set of sources, each with when it applies; the conductor puts in a packet only the sources that match it | Must-have | ✅ | ✅ | ✅ |
| R7 | The product context reaches the shaping and the frame when they open | Must-have | ✅ | ✅ | ✅ |
| R8 | The changelog adapts to the project | Must-have | ✅ | ✅ | ✅ |
| R8.1 | Labels, language and the layout of the decision lines are configurable; Flow's current format is the default | Must-have | ✅ | ✅ | ✅ |
| R8.2 | `changelog-propose` finds the work folder with its date | Must-have | ✅ | ✅ | ✅ |
| R9 | Flow ships no adapter for a specific service: the contract is public and another plugin implements it | Must-have | ✅ | ✅ | ✅ |

**Notes:**
- C fails R0.1: it would mirror an archive back into the repo.
- B fails R0.3, R3, R5: whether a forked `flow:docs` can invoke another skill and reach MCP connectors is unverified (B3, B4).
- C fails R2 and R3: a mirror is a second copy, stale between syncs, and a mirror folder in the repo tree invites committing it; a stale mirror is a territory Flow believes it reached.

## Selected shape

**A**, selected by the user on 2026-10-31. Shape A = A1 + … + A10. User rulings: sources are declared in `.flow.yml` (A6); an archived work keeps its folder of files on the territory and leaves no index row (A9).

## Fidelity check

2026-10-31, `flow:plan-reviewer`: 8 findings, all applied (R2 reduced to documentation by user ruling; Serves column added; R8.2 justified; `archive` op added to A2; A5, A7, A4 and R0.1 tightened).
