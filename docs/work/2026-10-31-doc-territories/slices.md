---
shaping: true
work: doc-territories
updated: 2026-10-31
tags: [work, doc-territories, slices]
description: "Slices of documentation territories: changelog, sources, the adapter contract, reads, writes, archive"
---

# Documentation territories — Slices

Shape: A in [shaping.md](shaping.md) (A1–A10). Breadboard skipped: no new places, every change lands in existing skills plus one contract document.

**Demo vehicle.** Flow ships no service adapter (R9). From V3 on, slices are demonstrated with a throwaway test adapter: a plugin created in the session scratchpad, loaded with `--plugin-dir`, whose territory is a folder outside the test repo and whose documents carry the key/value table instead of frontmatter (the fallback of `## Fields`; native fields are an adapter's choice and are not exercised here). It is never committed. A throwaway test repo, also outside this repo, holds the `.flow.yml` under test.

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
- `flow:docs` `changelog-propose` and `changelog-write` read the map; the defaults reproduce today's entry exactly.
- `changelog-propose` reads the work folder as `<root>/work/*-<slug>/` (dated), falling back to `<root>/work/<slug>/`.
- `/flow:changelog`, the README and the architecture name the new keys.

**Out**: a changelog on an external territory (V5 writes, through the adapter).

**Done**
- In a throwaway repo with `changelog: {labels: {work: Job, decision: Why}, decisions: inline, language: it}` and a dated work folder, `changelog-propose` reads the work's `STATUS.md` and `slices.md`, and `changelog-write` writes an entry in Italian with `**Job**` and one `**Why**` line joining two links with ` · `.
- With `changelog: docs/CHANGELOG.md` or no key, the entry is byte-for-byte today's format.
- Gate green.

**Gotchas**: `changelog` is read by `flow:docs`, `/flow:changelog` and the close in `shared/work.md`; all three must accept both forms.

## V2 — Guidelines as a set of sources

**In**
- `sources:` in `.flow.yml`: a flow-style list of entries, each a repo path and a `when`: path globs (`spec/**`) or step names (`frame`, `shaping`, `plan`, `review`, `execute`, `impact`).
- The conductor (`/flow:dev`, slice packet and definition phases) puts in a packet the sources whose `when` matches the slice's `files:` or the current step, verbatim or by path as the packet rules say.
- The guidelines template's `## References` stays prose; the template points at `sources:` for what must reach agents.
- README and architecture.

**Out**: sources on an external territory (V4).

**Done**
- In a throwaway repo with `sources:` naming a testing guide for `spec/**` and a pricing note for `plan`, the V1 packet of a work whose slice touches `spec/` holds the testing guide; a slice touching only `app/` does not; the planner's packet holds the pricing note.
- No `sources:` → packets are as today.
- Gate green.

**Gotchas**: packet size; a source is a whole file, so a long one weighs on every dispatch of the slice.

## V3 — The adapter contract and its resolution

**In**
- `territories:` in `.flow.yml`: one entry per kind (`decisions`, `changelog`, `guidelines`, `lenses`, `product`, `archive`), each `adapter`, `where`, `write`. Undeclared kind → the repo, under `<root>/`, as today.
- `shared/territories.md`: the contract (`## Requires`, `## Fields`: native fields when the service has them, else the key/value table, `## Operations`: `read`, `write`, `search`, `list`, `archive`, each with its input and return), and the repo as the contract's reference behaviour. A10's documentation lines on tokens and links live here and in the README.
- Resolution in the conductor, on the design adapter's pattern: the qualified skill name from `territories:`, invoked by the Skill tool, `## Requires` read first; a missing skill or unmet requirement stops the step and names the territory.
- `/flow:dev` profile: shows the declared territories at opening. (`/flow:project` homes have no `.flow.yml`: out.)

**Out**: any read or write through an adapter (V4, V5).

**Done**
- The throwaway test adapter, written only from `shared/territories.md`, resolves: opening a work in the test repo lists its territories.
- The test adapter's skill removed → the conductor stops and names the territory.
- No `territories:` → nothing changes at opening.
- Read-through: `shared/territories.md` and the README carry A10's lines on tokens, per-user values and share links.
- Gate green.

**Gotchas**: `presets:` already maps `design=<skill>`; keep `territories:` separate so a territory never reads as a stack.

## V4 — Reads through territories

**In**
- The conductor fetches through the adapter, for a declared territory, every read that ends in a packet or a `flow:docs` request: decisions' descriptions and `files` matches (from the key/value table), search hits, guidelines, impact lenses, sources that name a territory, product context.
- `flow:docs` `search` and `descriptions` accept excerpts the conductor passes, merging them with the repo's matches; its output format is unchanged.
- Product context (A7): read at the opening of the frame and the shaping, the excerpt placed above the path instruction. Undeclared → nothing read.
- Resume reloads the same reads.

**Out**: writes (V5).

**Done**
- With the test adapter serving `decisions`, `product` and `guidelines`, opening a work runs the memory search and shows a hit from the external folder; a slice packet carries the description of an external decision matched by `touches`, the external guidelines' sections and a `sources:` entry naming a territory; the frame and shaping prompts carry the product excerpt.
- A resume at `step: plan` reloads the same external reads.
- No `territories:` → the same packets as today.
- Gate green.

**Gotchas**: reads are not validated: `decisions-check` runs on writes (V5).

## V5 — Writes through territories

**In**
- Decision write and status change on an external `decisions` territory: `flow:docs` composes the content (fields and body) in a temporary file, runs `decisions-check` on it, and only then the conductor hands it to the adapter's `write`.
- The changelog on an external territory: `changelog-write` composes the entry, the adapter writes it; the version file stays in the repo and is committed.
- `written` or `proposed <link>`: a proposal is appended under `## Pending proposals` in `STATUS.md`; the close's report lists them in one line.

**Out**: archive (V6).

**Done**
- Test adapter with `write: comment` on `decisions`: recording a decision returns `proposed`, `STATUS.md` lists it, the close's report names it. With `write: direct`, the document appears in the external folder with its key/value table.
- A status change on an external decision updates its fields in the external folder.
- With `changelog` on the test territory, `changelog-write` puts the entry there; `.version` is bumped in the repo and committed.
- A decision that fails `decisions-check` is not handed to the adapter.
- No `territories:` → decisions and changelog are written and committed as today.
- Gate green.

**Gotchas**: `flow:docs` commits what it writes in the repo; for an external write there is nothing to commit, and the return line must say so.

## V6 — The archive on a territory

**In**
- `/flow:archive` with an `archive` territory: the adapter's `archive` op receives the work folder and keeps it as a folder of documents; the local folder is removed in the archive commit; no `archived.md` row, no wall.
- With no `archive` territory, `/flow:archive` works as today.

**Done**
- In the test repo, archiving a closed work copies its files into a folder of the external territory and removes the local one in one commit; `archived.md` is untouched and no `deny` is added; `search` on the archive territory finds the work.
- No `archive` territory → `/flow:archive` as today.
- Gate green.
