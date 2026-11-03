---
shaping: true
work: guidelines-folder
description: "Slices of the guidelines-folder work: the folder in the conductor, sources: removed with its upgrade, the guidelines territory made multi-document, Flow's own repo and docs adopting the folder."
---

# Guidelines folder — Slices

Mandate: Work 1 of [`docs/briefs/2026-11-03-guidelines-folder.md`](../../briefs/2026-11-03-guidelines-folder.md) ("What changes", Goals, Non-goals, Anti-goals, Calls, Left to the builder). The project is a plugin of Markdown skills: a slice is "done" when the text says what the brief says, the gate of `docs/development-guidelines.md` is green, and a read-through finds no skill contradicting another.

Order: V1 → V2 → V3 → V4. V1 adds the folder while `sources:` still exists; V2 removes `sources:` and adds its upgrade; V3 moves the folder onto a territory; V4 makes this repo and its docs the first user.

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Renaming a section means fixing every citation, in `shared/work.md`, `shared/territories.md` and `agents/slice-runner.md` too: `rg -n '§ Sources'` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks.
- `claude plugin validate .` warns about missing version and icon; those two warnings are expected, anything else is not.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).

---

## V1 — The folder in the conductor

**In.** `skills/dev/SKILL.md` learns `<root>/guidelines/`:

- Conventions table: a `Guidelines folder` row (`docs/guidelines/`, read after § Territories, the frontmatter keys, chosen per slice), next to the `Guidelines` row, which becomes the scheda's row and says the scheda travels whole.
- A `**Guidelines folder.**` paragraph in the configuration section (where § Sources sits; § Sources stays in V1): the two frontmatter keys (`description` required, `applies` optional: globs in the glob syntax, or `always`), a file with no description skipped and named, sub-folders free and meaningless to Flow, the three selection rules (always; globs matched against the slice's paths as § Sources matches today, re-matched after the plan against the planner's `Files:`; judgment on the description from the slice's section alone, never opening a file), the packet section `## Guidelines` with one `### <path>` per file after the scheda, the first stop's one line naming the chosen files with line counts, "moments without slice paths" (opening and resume, the criterion block for `shaping` and `framing-doc`, the fidelity check) getting the scheda plus `always` files.
- The slice packet paragraph (Step 4), impact's packet (Step 3), the review's re-match (step: review), the first stop's message, the resume tables (`plan` and `review` rows, and the always-reloaded row) and § The criterion travels: each names the folder's contribution.
- Profile › The draft: the draft writes the four sections only (no "observed conventions"); § Step 0's message gains one sentence naming the folder and its two keys, no detection.
- Cost discipline rule 1 and Project memory's table name the folder.
- `skills/dev/development-guidelines.template.md`: a top note saying prose about naming, placement and tests goes into `<root>/guidelines/` files with the frontmatter, the scheda keeps the four sections.

**Out.** Removing `sources:` (V2). Territories (V3). This repo's own files and the README (V4). `agents/slice-runner.md` is untouched: the runner reads the packet file and the folder's files are already in it.

**Done.**

- `claude plugin validate .` passes with only the two expected warnings.
- `rg -n 'guidelines/' skills/dev/SKILL.md` hits the Conventions row, the configuration paragraph, the packet paragraph, impact, review, the resume table, Step 0 and The draft.
- `rg -n -c 'observed conventions' skills/dev/SKILL.md` prints nothing.
- Read-through of the diff against the brief's "The folder", "The scheda stays", "Selection", "Moments without slice paths", "First run": each sentence of the brief has its place in the skill, and no two paragraphs give a different rule for the same moment.
- Gate green.

**Gotchas.** The resume table's `plan` row today says "the sources matched by the slice's paths": V1 adds the folder next to it, V2 removes the sources part. The first stop today says `sources in packet: none` when none: V1 adds the guidelines line alongside; V2 drops the sources one. Keep both in V1 so the skill is consistent at every commit.

---

## V2 — `sources:` goes away, with its upgrade

**In.**

- `skills/dev/SKILL.md`: § Sources removed; the `sources` key leaves the configuration sentence; every citation (`§ Sources`, `## Sources for step:`, `## Sources for phase:`, `Execute sources:`, `Close sources:`, `sources in packet`, "the sources matched", "when `.flow.yml` has `sources:`", `phase: impact` block, § The criterion travels) rewritten or removed. The glob syntax paragraph moves into the folder paragraph, since `applies` is its only user now.
- `shared/work.md`: § Agents › Dispatch contract loses "then the step's sources"; § Upgrade gains the case: the check block prints `sources:` lines from `.flow.yml` on the default branch (`git grep -n '^sources:' <default> -- .flow.yml`); the Rewritten table gets the rows of the brief's "Upgrade" (an entry with a glob → `applies` and a drafted `description` in the file's frontmatter, `git mv` under `<root>/guidelines/` when not there, globs of entries sharing a path merged; an entry with no glob left → dropped and named, the file stays; a `{territory, name}` entry → dropped and named; the key removed); the Pointed-at list gets inbound references to a moved file (`@` imports in `CLAUDE.md` files, Markdown links) and the scheda's sections beyond the four as candidates for the folder.
- `agents/slice-runner.md`: the `Execute sources:` and `Close sources:` fields and their uses go.
- `shared/territories.md`: § Reads › "Sources naming a territory" removed, with its two table rows; "Packet place" loses "or a matched source names a territory".
- `README.md`: the `sources` line leaves the `.flow.yml` example and its paragraph; the Customizing table row "Give agents several documents" and the "Several files for one stack" example point at the folder instead (V4 rewrites the section; V2 only stops it lying). `docs/architecture.md`: `sources` leaves the `.flow.yml` row and the slice-loop bullet.

**Out.** The territory's multi-document reads (V3). The README section's rewrite and this repo's files (V4).

**Done.**

- `rg -n -i 'sources' skills agents shared README.md docs/architecture.md` matches only `shared/work.md` § Upgrade and the README's § Upgrading from an older Flow (and `docs/design/` "HTML sources" in architecture.md, a different word).
- The upgrade's check block in `shared/work.md`, run by hand on this repo's `main`, prints `upgrade check: done` and nothing else (this repo has no `sources:`), and prints the `sources:` line when run against a throwaway branch whose `.flow.yml` has one.
- `claude plugin validate .` passes; gate green.
- Read-through: `skills/dev/SKILL.md` and `agents/slice-runner.md` agree on the dispatch (packet, then the role's instruction, nothing between).

**Gotchas.** The README's § Upgrading and `shared/work.md` § Upgrade are the only places allowed to keep old names, by the gate's old-names check: `sources` isn't an old name in that check, so nothing to add there, but the two sections must say the same thing. `git mv` in the upgrade runs on the `flow-upgrade` branch, as the rest of the upgrade does; the drafted description must be marked so the user rewrites it (the brief: "marked for the user to rewrite").

---

## V3 — Guidelines on a territory, many documents

**In.** `shared/territories.md`:

- Kinds table: `guidelines` → `<root>/development-guidelines.md` and `<root>/guidelines/`.
- § Fields: `applies` added (a list of globs, or `always`), with its frontmatter form.
- § Operations: `op: list` and `op: read; name:` for `guidelines`; `op: read` with no `name` keeps returning the scheda, so the single-document rows for `guidelines` stay true; the `list` line format for `guidelines` carries name, description and `applies`.
- § Reads: the rows for `guidelines` (opening or resume: `read` of the scheda plus `list`; impact and the slice packet: `list`, then `read` with `name` for the chosen documents; after the plan: `read` for a newly matched one), and a "Guidelines on a territory" paragraph that says `where` keeps meaning what the project declared and the adapter decides how the scheda and its siblings sit there; selection works from the `list` lines as from the frontmatter.
- § Resolution: an `unmet` on `op: list` for `guidelines` stops the run like any other unmet, with the message naming the adapter and the contract change. No fallback; the brief's owner decision.
- `skills/dev/SKILL.md`: the folder paragraph and the packet paragraph say "a declared `guidelines` kind's documents come from its adapter (`list`, then `read`)".

**Out.** Any adapter (none is Flow's). Mixed repo-and-territory folders (non-goal).

**Done.**

- `rg -n 'single-document kinds' shared/territories.md` shows a list without `guidelines` for `list`, and the `op: read` row says what no `name` returns for `guidelines`.
- Read-through of `shared/territories.md` alone, as its first paragraph demands ("someone who has read only it can write an adapter"): an adapter author can tell what `list` must return for `guidelines`, what `read` with and without `name` returns, and what happens when their adapter is old.
- `claude plugin validate .` passes; gate green.

**Gotchas.** The contract says "one invocation per adapter per step": the `list` and the `read`s of the chosen documents are two steps of the same moment (the list must come back before the names are known), so the paragraph must say whether that is one invocation with `list` and a second with the `read`s, or two lines in one; `decisions` already does `search` plus `list` in one invocation and reads matched files after, which is the model to copy. The `## Tests` write (§ Writes) keeps targeting the scheda: say so where the write is described.

---

## V4 — This repo adopts the folder; the docs show it

**In.**

- `docs/development-guidelines.md` keeps `## Tests`, `## Gate`, `## References` (added, naming the brief and the skills) and `## Deviations` (only if there is content); `## Naming`, `## Language`, `## Commits` and `## Releases` become files under `docs/guidelines/` (`naming.md`, `language.md`, `commits.md`, `releases.md`), each with a `description` and an `applies` where one is natural (`commits.md` and `releases.md`: `always`; `naming.md`: `["skills/**", "agents/**", "shared/**"]`; `language.md`: `always`), text unchanged.
- `skills/dev/impact-lenses.template.md`: two example lenses in its comments (multi-tenant ids; a DatoCMS sandbox that costs money), and one line on the difference from a rule.
- `README.md`: the "Customizing a stack" section rewritten around the folder: where a stack's rules live (preset), where yours live (the folder, two frontmatter lines), one example with three Rails files, how to replace a preset. Short: Work 2 will cut the README further. `docs/architecture.md`: `docs/guidelines/` row in the "What a project holds" table and node in its diagram; the scheda's row says what it holds now; `site/index.html`'s project tree gains `guidelines/`.
- `docs/CHANGELOG.md`: nothing here; the close writes it through `flow:docs`, naming the contract change for adapter authors.

**Out.** The README's full rewrite and `docs/configuration.md` (Work 2). The site's text beyond the tree.

**Done.**

- `for f in docs/guidelines/*.md; do head -1 "$f" | grep -qx -- '---' && grep -q '^description:' "$f" || echo "bad: $f"; done` prints nothing.
- `rg -n '^## ' docs/development-guidelines.md` shows Tests, Gate, References and, at most, Deviations.
- Gate green from the repo root: validate, old names, links (the moved sections' inbound links, if any, rewritten: `rg -n 'development-guidelines.md#' --glob '!docs/work/archive/**' .`).
- `rg -n 'guidelines/' README.md docs/architecture.md site/index.html` hits each.
- Read-through of the README section as the external developer of the brief: the three questions he asked (where the stack's guidelines live, where mine go, how I give several files) are answered in that order.

**Gotchas.** `## Gate` and `## Releases` reference each other today (the release rules are read before merging): `releases.md` must stay `always` or the close loses them. The README's `development-guidelines.md#releases` anchor is cited in the "Develop the plugin" section: move it to the new file. The site's tree is inside a `<pre>`: keep the indentation.
