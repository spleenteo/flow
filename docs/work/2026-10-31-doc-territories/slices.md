---
shaping: true
work: doc-territories
updated: 2026-10-31
tags: [work, doc-territories, slices]
description: "Slices of documentation territories: changelog, sources, the adapter contract, reads, writes, archive"
---

# Documentation territories — Slices

Shape: A in [shaping.md](shaping.md) (A1–A10). Breadboard skipped: no new places, every change lands in existing skills plus one contract document.

**Demo vehicle.** Flow ships no service adapter (R9). From V3 on, slices are demonstrated with a throwaway test adapter: a plugin created in the session scratchpad whose territory is a folder outside the test repo and whose documents carry the key/value table instead of frontmatter (the fallback of `## Fields`; native fields are an adapter's choice and are not exercised here). The session loads both the clone and the adapter, with `--plugin-dir` given twice: with one only, the released `flow` runs. Neither the adapter nor the throwaway test repo holding the `.flow.yml` under test is ever committed.

| Slice | Parts | Requirements |
|---|---|---|
| V1 | A8 | R8.1, R8.2 |
| V2 | A6 | R6 |
| V3 | A1, A2, A3, A10 | R0.1 (kinds), R0.2, R1, R2, R9, R3 (stop) |
| V4 | A4 (reads), A7 | R0.3 (read, search), R3, R5 (query), R7 |
| V5 | A4 (writes), A5 | R0.3 (write), R4, R5 (write) |
| V6 | A9 | R0.1 (archive) |

## V1 — The changelog adapts to the project

**In**
- `changelog:` in `.flow.yml` accepts a map: `path`, `labels` (`work`, `decision`), `language`, `decisions: lines | inline` (`inline`: one bullet, links joined by ` · `). A string keeps today's meaning, as `path`.
- Every reader of the key accepts both forms and takes `path` from the map: `skills/docs/SKILL.md` (configuration table, `changelog-propose`, `changelog-write`, the entry rules, `search`), `shared/work.md` (archive step 2), the key list in `skills/dev/SKILL.md`, the README and `docs/architecture.md`. The defaults reproduce today's entry exactly.
- `changelog-propose` finds the work folder with `rg -l '^work: <slug>$' <root>/work/*/STATUS.md` (dated or not), never a `*-<slug>` glob, which also matches `mobile-menu` for `menu`.

**Out**: a changelog on an external territory (V5).

**Done**
- In a throwaway repo with `changelog: {labels: {work: Job, decision: Why}, decisions: inline, language: it}` and a dated work folder, `changelog-propose` reads the work's `STATUS.md` and `slices.md`, and `changelog-write` writes an entry in Italian with `**Job**` and one `**Why**` line joining two links with ` · `.
- Two works `menu` and `mobile-menu`: `changelog-propose` for `menu` reads only its own folder.
- With `changelog: docs/CHANGELOG.md` or no key, the entry is byte-for-byte today's format.
- Gate green.

**Deviations found while planning V1**
- The work-folder lookup gets the `<dir>/` prefix `flow:docs` requires on every `rg` path; no match continues from the git facts, several matches return `FAILED`.
- `shared/work.md` line 8 ("Keys this file uses") also gains `changelog`, since archive step 2 reads it.

**V1 — done on 2026-10-31**

The changelog key takes a path or a map (`path`, `labels.work`, `labels.decision`, `language`, `decisions: lines | inline`); `flow:docs` defines `<changelog>` once and every reader (archive step 2, the dev key list, README, architecture) points to it. The work folder is found by `rg -l '^work: <slug>$' <dir>/<root>/work/*/STATUS.md` (no match: git facts only; several: FAILED). In scratchpad repos the map config gave an Italian entry with one `**Job**` and one `**Why**` line joining two decisions with ` · `; propose for `menu` read only its own dated folder; the string and no-key configs matched main's Flow structure line for line. The entry template was left untouched, which kept the default identical. All four tasks passed on the first review; no rulings. Left low: the lookup lacks `2>/dev/null` for an empty `work/`; the `<changelog>` sentence omits its default; archive's `rg "<slug>" <changelog>` still matches `mobile-menu` for `menu` (carried into V6).

## V2 — Guidelines as a set of sources

**In**
- `sources:` in `.flow.yml`: a flow-style list of entries, each a repo path and a `when`, which holds path globs (quoted, since a flow-style value starting with `*` is a YAML alias) or `phase:`/`step:` values: the phases `frame`, `shaping`, `breadboard`, `design`, `slicing`, `impact`, and the slice steps `plan`, `review`, `execute`, `close`.
- Path-scoped sources go in the slice packet: matched against the paths the slice's section of `slices.md` names, and again after the plan against the planner's `Files:`, as the decisions already are; a new match is added to the packet before the review.
- Step-scoped sources go in the instruction after the packet, for the dispatches of that step only, so the packet stays one verbatim prefix for the whole slice.
- The guidelines template's `## References` stays prose; the template points at `sources:` for what must reach agents.
- The key list in `skills/dev/SKILL.md`, the README and `docs/architecture.md`.

**Out**: sources on an external territory (V4).

**Done**
- In a throwaway repo with `sources:` naming a testing guide for `"spec/**"` and a pricing note for `plan`: the packet of a slice whose section names `spec/` holds the testing guide; a slice naming only `app/` does not; the planner's instruction, and no other dispatch, carries the pricing note.
- No `sources:` → packets and instructions are as today.
- Gate green.

**Gotchas**: packet size; a path-scoped source is a whole file and weighs on every dispatch of the slice.

**Deviations found while planning V2**
- The first stop gets one line naming the sources the packet carries and their line counts (the packet-size gotcha).
- `execute` and `close` sources reach the runner through two new fields, `Execute sources:` and `Close sources:`, and the dispatch contract in `shared/work.md` changes.
- A directory path matches a glob only through the glob's literal prefix; a glob starting with a wildcard never matches a directory.

**V2 — done on 2026-10-31**

`sources:` in `.flow.yml` lists files that must reach agents, each with a `when` that is a quoted glob or a `{step:}`/`{phase:}` value. Sources matched by path ride in the packet's `## Sources` and are matched again against the planner's `Files:` before the review. Step sources ride in a `## Sources for step: <name>` block after the packet, only for that step's dispatches. The runner gets them as `Execute sources:`/`Close sources:` fields. The skill's example uses the brace form `when: {step: plan}` rather than the plan's `[step: plan]`, by a ruling, and the README copies it. T4's check that printed prompts through `claude -p` was flagged by the safety classifier. It was BLOCKED, and by the user's answer became a routing table: which source files each dispatch carries, in packet or in instruction. All five runs pass. With no `sources:`, V2's Flow and the 57b9c30 baseline route identically. The final review found that an untracked file (a `.env`) could pass the path check and land in the committed packet. Sources must now be tracked by git (`git ls-files --error-unmatch`). The same fix round made headings repo-relative and tightened the field conditions. Four low wording nits remain logged.

## V3 — The adapter contract and its resolution

**In**
- `territories:` in `.flow.yml`: one entry per kind (`decisions`, `changelog`, `guidelines`, `lenses`, `product`, `archive`), each `adapter`, `where`, `write`. Undeclared kind → the repo, under `<root>/`, as today. When `territories: changelog` is declared it wins over `changelog.path`, and the opening says so; `labels`, `language` and `decisions` still apply.
- `shared/territories.md`: the contract.
  - `## Requires`; `## Fields`: native fields when the service has them, else the key/value table; `## Operations`: `read`, `write` (`written` or `proposed <link>`), `search`, `list`, `archive`, each with its input and return.
  - `search` and `list` take keywords and files and return at most ten lines, or write their result to a scratch file whose absolute path they return.
  - An adapter has no `context: fork` (it would bring back the fork-to-connector path the decision rules out) and no `disable-model-invocation: true` (the Skill tool could not call it).
  - The repo as the contract's reference behaviour; A10's lines on tokens, per-user values and share links (also in the README).
- Resolution in the conductor, on the design adapter's pattern: the qualified skill name from `territories:`, invoked by the Skill tool, `## Requires` read first; a missing skill or unmet requirement stops the step and names the territory. Adapters resolve **before the profile**, since the profile may read a guidelines territory. One adapter invocation per step.
- `/flow:dev` profile: shows the declared territories at opening. (`/flow:project` homes have no `.flow.yml`: out.) The key list in `skills/dev/SKILL.md` and `skills/docs/SKILL.md`'s configuration table, `docs/architecture.md`.

**Out**: any read or write through an adapter (V4, V5).

**Done**
- The throwaway test adapter, written only from `shared/territories.md`, resolves: opening a work in the test repo lists its territories. This is the first real check that a skill of another plugin is called by qualified name.
- The test adapter's skill removed → the conductor stops and names the territory.
- No `territories:` → nothing changes at opening.
- Read-through: `shared/territories.md` and the README carry A10's lines and the two forbidden frontmatter keys.
- Gate green.

**Gotchas**: `presets:` already maps `design=<skill>`; keep `territories:` separate so a territory never reads as a stack. Carried from V2's final review (low): in `skills/dev/SKILL.md` ~:297 the planner's step block depends on any `sources:` being declared instead of on a `step: plan` source; fix it while editing that file. Routing for `review`, `execute` and `close` sources was never exercised in V2's test repos.

**Deviations found while planning V3**
- A malformed `territories:` entry (unknown kind, or no `adapter` or `where`) also stops the run and names the entry.
- `op: none` is added beside the five operations: it runs `## Requires` alone, and it is the only request V3 sends.
- V2's low finding is fixed at ~:297 and at the same defect in the review block (~:305), the phase sources (~:269) and the impact block (~:279).
- "One adapter invocation per step" reads as one invocation per adapter per step: territories served by different adapters can't share one Skill call.
- A run for an unmet `## Requires` is added to the checks.

**V3 — done on 2026-10-31**

`territories:` in `.flow.yml` declares, per kind (`decisions`, `changelog`, `guidelines`, `lenses`, `product`, `archive`), an `adapter` skill of another plugin, a `where` and a `write` word; an undeclared kind stays in the repo. `shared/territories.md` is the contract: declaring, resolution and its stops (malformed entry, missing skill, `unmet`, `unreachable`, never a fallback to the repo), the request lines (one per line, `; `-separated pairs), the adapter skill's sections and the forbidden `context: fork` and `disable-model-invocation: true`, the five ops plus `op: none`, the repo as reference behaviour, and A10's lines on tokens and share links. `/flow:dev` resolves territories after the upgrade check with one `op: none` Skill call per adapter and lists them at opening; flow:docs, the README and the architecture document the key. A throwaway adapter written from the contract alone exposed two gaps the final review fixed: how several requests reach one adapter, and how `write name: new` returns its id. Headless runs proved that another plugin's skill is called by its qualified name: run 1 lists the three territories, run 2 (skill removed) and run 3 (unmet `where`) stop naming the territory, and without the key the opening matches the baseline. The read order did not hold: the model batches `.flow.yml` with the guidelines and changelog in one early shell call, before the adapters resolve, even after a "read only `.flow.yml`" rule was added. By a ruling it is carried to V4, where a guidelines territory makes it matter. V2's low on `step: plan` source conditions is fixed at four places.

## V4 — Reads through territories

**In**
- The conductor fetches through the adapter, for a declared territory, every read that ends in a packet, an instruction or a `flow:docs` request:
  - decisions: search hits and descriptions at opening, at resume, at impact, in the slice packet, and in the re-query after the plan; the `touches` match on external decisions is done by the conductor from the fields the adapter returns;
  - guidelines and impact lenses, including the profile's `## Tests` and `## Gate`, read before the profile;
  - sources that name a territory;
  - the changelog's past entries, read by `changelog-propose` and `changelog-write` for tone and language;
  - product context (A7): at the opening of the frame and the shaping, above the path instruction; undeclared → nothing read.
- `flow:docs` `search`, `descriptions` and the changelog ops take an `excerpts:` header key, the absolute path of a scratch file the conductor wrote; they merge it with the repo's matches; output format unchanged.
- Step 0 offers no local guidelines or lenses file for a kind declared on a territory.
- A packet that carries territory excerpts is written to the session scratchpad, not to `<work dir>/V<n>-packet.md`, and is never committed; the ledger records its path.
- Docs: `docs/architecture.md`, the resume table and the project-memory table in `skills/dev/SKILL.md`, the `root:` comment in the README.

**Out**: writes (V5).

**Done**
- With the test adapter serving `decisions`, `product` and `guidelines`: opening a work shows a memory-search hit from the external folder and the profile's test level from the external guidelines; a slice packet carries the description of an external decision matched by `touches`, the external guidelines' sections and a `sources:` entry naming a territory; the frame and shaping prompts carry the product excerpt.
- The slice's packet file is in the scratchpad and `git status` shows no packet under the work folder.
- A resume at `step: plan` reloads the same external reads.
- No `territories:` → the same packets, in the same place, as today.
- Gate green.

**Gotchas**: reads are not validated: `decisions-check` runs on writes (V5). Carried from V3 (ruling): the read order doesn't hold headless — the model batches `.flow.yml`, the guidelines and the changelog in one early shell call, before adapters resolve. Fix here: the upgrade check matches its traces without printing any file; "read `.flow.yml` alone" becomes its own step at `skills/dev/SKILL.md` ~:116 and ~:120; the "Read if present" lines at ~:42 and ~:49 apply only after territories resolve; verify with a grep on the order of tool calls. Context: one adapter call per step, results capped or in a scratch file.

## V5 — Writes through territories

**In**
- Decisions on an external territory, from every writer (the conductors and `flow:grill`): `flow:docs` composes the decision as a dated file alone in a scratch folder whose absolute path the request gives, runs `decisions-check` on that folder, and returns the path with no sha; the conductor hands it to the adapter's `write`. A status change first reads the external decision into such a file, then the same.
- The changelog on an external territory: `changelog-write` composes the entry from the excerpt of past entries (V4), the adapter writes it, and the commit holds only the version file. A **Decision** line links the external document by an identifier or a login-only link; it is not dropped because `decisions-log/` is missing.
- Profile writes to an external guidelines territory (the `## Tests` lines) go through the adapter and may come back `proposed`.
- `written` or `proposed <link>`: a proposal is appended under `## Pending proposals` in `STATUS.md` (added to the template in `shared/work.md`); the close's report lists them in one line, its sequence unchanged.
- Docs: `docs/architecture.md`, `skills/docs/SKILL.md`'s request table (`excerpts:`, scratch path, no-sha returns).

**Out**: archive (V6).

**Done**
- Test adapter with `write: comment` on `decisions`: recording a decision returns `proposed`, `STATUS.md` lists it, the close's report names it. With `write: direct`, the document appears in the external folder with its key/value table.
- A decision recorded from `flow:grill` lands on the territory too.
- A status change on an external decision updates its fields in the external folder.
- With `changelog` on the test territory, `changelog-write` puts the entry there with its Decision line; `.version` is bumped in the repo and committed alone.
- A decision that fails `decisions-check` is not handed to the adapter.
- No `territories:` → decisions and changelog are written and committed as today.
- Gate green.

## V6 — The archive on a territory

**In**
- `/flow:archive` with an `archive` territory: the adapter's `archive` op receives the work folder and keeps it as a folder of documents; the local folder is removed in the archive commit; no `archived.md` row, no wall.
- Inbound links to the work's folder (from decisions, the changelog, other works) become plain text naming where it went (`archived on <territory>: <name>`), never a share link, so the Gate's link check stays green.
- The slug check at opening and the opening memory search also query the archive territory (`list`, `search`).
- With no `archive` territory, `/flow:archive` works as today.
- Docs: `shared/work.md` § Archive, `docs/architecture.md`, `skills/dev/SKILL.md` where it cites `archived.md`.
- Archive step 2's `rg "<slug>" <changelog>` matches longer slugs (`mobile-menu` for `menu`): anchor it on the Work bullet (from V1's final review). The site's file tree (`site/index.html`) shows the default and stays.

**Done**
- In the test repo, archiving a closed work copies its files into a folder of the external territory and removes the local one in one commit; `archived.md` is untouched and no `deny` is added; a decision linking the work now names it in plain text; the Gate's link check is green.
- Opening a new work with the archived work's slug is caught; the opening search finds the archived work.
- No `archive` territory → `/flow:archive` as today.
- Gate green.
