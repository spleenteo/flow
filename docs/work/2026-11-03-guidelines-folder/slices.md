---
shaping: true
work: guidelines-folder
description: "Slices of the guidelines-folder work: the folder in the conductor, sources: removed with its upgrade, the guidelines territory made multi-document, Flow's own repo and docs adopting the folder."
---

# Guidelines folder — Slices

Mandate: Work 1 of [`docs/briefs/2026-11-03-guidelines-folder.md`](../../briefs/2026-11-03-guidelines-folder.md) ("What changes", Goals, Non-goals, Anti-goals, Calls, Left to the builder). The project is a plugin of Markdown skills: a slice is "done" when the text says what the brief says, the gate of `docs/development-guidelines.md` is green, and a read-through finds no skill contradicting another.

Order: V1 → V2 → V3 → V4. V1 adds the folder while `sources:` still exists; V2 removes `sources:` and adds its upgrade; V3 moves the folder onto a territory; V4 makes this repo and its docs the first user.

Impact (2026-11-03) ran two agents on these slices; its 23 findings are folded into the items below. No slice was split or reordered.

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Other files carry their own strings: `shared/work.md:208` cites `§ Sources`; `agents/slice-runner.md` carries `Execute sources:` and `Close sources:`; `shared/territories.md` has "Sources naming a territory" and "a matched source names a territory". Renaming or removing one means fixing every citation: `rg -n -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' skills shared agents` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks. The link check strips anchors (`${t%%#*}`): a dead `#section` passes green, so anchors are checked by hand.
- `claude plugin validate .` passes with the warnings § Gate lists (missing version, missing icon, the folder also holding a marketplace manifest) plus OS-metadata warnings for files `git ls-files` doesn't print (`.DS_Store`); anything else is a finding.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).
- This session runs the installed plugin copy (0.5.0), not this clone. A Done item that needs the edited conductor to run uses a clone of this repo in the session scratchpad with `claude --plugin-dir <clone>` on a throwaway repo, never this checkout and never a branch created here (`docs/development-guidelines.md` § Tests and § Releases).

**Gate narrowing for this work** (ruled at V1's close, 2026-11-04): the link check of § Gate runs with `--glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md'` added to its `rg --files` call. A packet copies `docs/development-guidelines.md` whole, so its relative links never resolve from the work folder. V4 makes `--glob '!docs/work/**/*-packet.md'` permanent in § Gate, and this narrowing ends with the work.

---

## V1 — The folder in the conductor

**In.** `skills/dev/SKILL.md` learns `<root>/guidelines/`:

- Conventions table: a `Guidelines folder` row (`docs/guidelines/`, read after § Territories, the frontmatter keys, chosen per slice), next to the `Guidelines` row, which becomes the scheda's row and says the scheda travels whole.
- A `**Guidelines folder.**` paragraph in the configuration section (where § Sources sits; § Sources stays in V1): the two frontmatter keys (`description` required, `applies` optional: globs in the glob syntax, or `always`), a file with no description skipped and named, sub-folders free and meaningless to Flow, the three selection rules (always; globs matched against the slice's paths as § Sources matches today, re-matched after the plan against the planner's `Files:`; judgment on the description from the slice's section alone, never opening a file), the packet section `## Guidelines` with one `### <path>` per file after the scheda, the first stop's one line naming the chosen files with line counts (next to today's `sources in packet` line, which V2 drops), "moments without slice paths" (opening and resume, the criterion block for `shaping`, `framing-doc`, `breadboarding` and `/flow:design`, the fidelity check of Step 5 move 1) getting the scheda plus `always` files. With a `guidelines` territory declared, the repo folder is not read (V3 brings the adapter path): no mixed state at any commit.
- **The scheda travels whole.** Every sentence that selects sections inside the guidelines says "the scheda, whole" plus the folder's share: `SKILL.md:42` ("its relevant sections"), `:90` ("the guideline sections that apply"), `:190` (the always-reloaded resume row), `:272` (§ The criterion travels), `:296` (the slice packet), `:369` (the fidelity check's packet); `shared/work.md:138` ("style preferences (repo: `development-guidelines.md`)") says the folder.
- The slice packet paragraph (Step 4), impact's packet (Step 3), the review's re-match (step: review), the resume tables (`plan` and `review` rows) and the first stop's message each name the folder's contribution.
- Profile › The draft: the draft writes the four sections only (no "observed conventions"). § Step 0: the `ls` and the collision sentence include `docs/guidelines` (a docs site may already have one: reported, never migrated); the message gains one sentence naming the folder and its two keys, skipped when `guidelines` is on a territory.
- Cost discipline rule 1 and Project memory's table name the folder.
- `skills/dev/development-guidelines.template.md`: a top note saying prose about naming, placement and tests goes into `<root>/guidelines/` files with the frontmatter, the scheda keeps the four sections; the `## Tests` comment stops inviting test prose (the two profile lines, framework and command only).
- `shared/territories.md:23`: the undeclared `guidelines` kind is the scheda and the folder (the kinds table; the rest of the contract waits for V3).

**Out.** Removing `sources:` (V2). The territory's reads (V3). This repo's own files and the README (V4). `agents/slice-runner.md` is untouched: the runner reads the packet file and the folder's files are already in it.

**Done.**

- `claude plugin validate .` passes with only the expected warnings (shared gotchas).
- `rg -n 'guidelines/' skills/dev/SKILL.md` hits the Conventions row, the configuration paragraph, the packet paragraph, impact, review, the resume table, Step 0 and The draft.
- `rg -n -c 'observed conventions' skills/dev/SKILL.md` prints nothing.
- `rg -n -i 'sections that apply|relevant sections|guideline sections|applicable guidelines|bear on it' skills/dev/SKILL.md` prints nothing.
- Read-through of the diff against the brief's "The folder", "The scheda stays", "Selection", "Moments without slice paths", "First run": each sentence of the brief has its place in the skill, and no two paragraphs give a different rule for the same moment.
- Gate green.

**Gotchas.** The resume table's `plan` row today says "the sources matched by the slice's paths": V1 adds the folder next to it, V2 removes the sources part. The first stop today says `sources in packet: none` when none: V1 adds the guidelines line alongside; V2 drops the sources one. Keep both in V1 so the skill is consistent at every commit.

**V1 — done on 2026-11-04**

The conductor learned docs/guidelines/: a **Guidelines folder.** paragraph beside § Sources (listing by rg plus one frontmatter awk, the two keys, three selection rules, the ## Guidelines packet section, the guidelines in packet: line at the first stop, the moments without slice paths, the territory condition), and every sentence that picked sections of docs/development-guidelines.md now passes it whole. The draft and Step 0 name the folder; the template sends prose to it; shared/work.md and shared/territories.md follow. T1's critical review found the /flow:design args stated two ways, a brief sentence (a wrong choice fixed by rewriting a description, never by configuration) with no place, and a "judged once" rule naming two moments; one fix round closed them. Ruled: shared/work.md:138 points style preferences at the folder only, as the plan said. The final review found the real cost of passing the guidelines file whole: its relative links break inside every committed packet, so the link check went red; the user chose to narrow the gate for this work, with V4 making the packet exclusion permanent. Left as follow-ups: skills/design/SKILL.md:14 doesn't list the guidelines in the called-mode args; the frontmatter awk should print the closing --- so an unclosed block is visible.

Commits: 335788f, 398a6ab, 57adaf2, ec1b866, f52d4ad, 0d7d8c3, b13d738. Gate green with this work's packet narrowing.

**Deviations found while planning V1**

- `skills/dev/SKILL.md:59` (inside § Sources) is edited too, though the slice's list doesn't name it: its "after the guideline sections" would fail the Done's `rg 'guideline sections'` check, and the packet order changes at that line.

---

## V2 — `sources:` goes away, with its upgrade

**In.**

- `skills/dev/SKILL.md`: § Sources removed; the `sources` key leaves the configuration sentence; every citation rewritten or removed: `§ Sources`, `## Sources for step:`, `## Sources for phase:`, `Execute sources:`, `Close sources:`, `sources in packet`, "the sources matched" (resume `plan` row), "when `.flow.yml` has `sources:`", the `phase: impact` block, § The criterion travels, `:328` "or a matched source names a territory", and "the `step: execute` block" at `:346` and `:349`. The glob syntax paragraph moves into the folder paragraph, since `applies` is its only user now. `:104` and `:116` say what the upgrade may read (below).
- `shared/work.md`: § Agents › Dispatch contract loses "then the step's sources". § Upgrade gains the case:
  - its first sentence (`:387`, "rewrites the ones Flow owns and points at the ones it doesn't") also covers the files `sources:` names;
  - the check block gains one line, `git grep -n '^sources:' <default> -- .flow.yml` (lines only, no file content), and the `work/*` loop also prints a work branch whose `.flow.yml` has `sources:`; nothing else becomes a trace: the scheda's extra sections and inbound references are named only inside an upgrade that `sources:` triggered;
  - at Asking, the entries are read with `git show <default>:.flow.yml` (block style puts them below the key line), and each drafted `description` comes from `git grep -m1 -n '^#' <default> -- <path>` (a line, never the file); both reads happen after the check and only on the ok, and `SKILL.md:104`/`:116` allow them there;
  - the Rewritten table gets the rows of the brief's "Upgrade": an entry with at least one glob → `applies` with the globs and the drafted `description`, marked for the user to rewrite, merged into an existing frontmatter block (an existing `description` is kept), and the file moved with `git mv` to `<root>/guidelines/<basename>` when it isn't there already, globs of entries sharing a path merged into one `applies`; a basename collision is named and the entry is not moved; an entry with no glob left → dropped and named, the file stays; a `{territory, name}` entry → dropped and named; a missing or untracked path → dropped and named; the key removed;
  - the commit's pathspec holds both the source and the destination of every move, so the staged deletion doesn't follow the switch back;
  - the Pointed-at list gets inbound references to a moved file (`@` imports in `CLAUDE.md` files, Markdown links), a moved file's outbound relative links that break, and the scheda's sections beyond the four as candidates for the folder;
  - Resume on an old file (b) covers `sources:` on the work's branch; the case is repo-only (`/flow:project` runs the same § Upgrade and has no `sources:`).
- `agents/slice-runner.md`: the `Execute sources:` and `Close sources:` fields and their uses go.
- `shared/territories.md`: § Reads › "Sources naming a territory" removed, with its two table rows (`:81`, `:83`) and the packet heading rule at `:108` (V3 restates the heading for guidelines documents); "Packet place" loses "or a matched source names a territory".
- `skills/dev/development-guidelines.template.md:7`: the `## References` comment points at `<root>/guidelines/` instead of `sources:`.
- `README.md`: the `sources` line leaves the `.flow.yml` example and its paragraph; the Customizing table row "Give agents several documents" and the "Several files for one stack" example point at the folder instead (V4 rewrites the section; V2 only stops it lying); § Upgrading from an older Flow, item 4, gains the `sources:` conversion in the same words as § Upgrade. `docs/architecture.md`: `sources` leaves the `.flow.yml` row and the slice-loop bullet.

**Out.** The territory's multi-document reads (V3). The README section's rewrite and this repo's files (V4).

**Done.**

- ``rg -n -i '\bsources?\b|step: (execute|close|plan|review)` block' skills agents shared README.md docs/architecture.md`` matches only: `shared/work.md` § Upgrade; the README's § Upgrading from an older Flow; and these unrelated uses, listed here as the allowed set: `skills/dev/SKILL.md` "When sources disagree" (profile) and "sources, transcripts" (the criterion), `skills/project/SKILL.md` "sources, transcripts", `skills/stack-astro/SKILL.md` "Content sources", `docs/architecture.md` "plugin sources" (a link) and "HTML sources".
- The upgrade's check block, run read-only against this repo's `main`, prints `upgrade check: done` and nothing else (no `sources:` here); run against a throwaway repo in the session scratchpad (`git init`, a `.flow.yml` with `sources:`), it prints the `sources:` line. Run against a throwaway repo whose scheda has `## Naming` and no `sources:`, it prints only `upgrade check: done`.
- With a clone of this repo in the scratchpad at this branch and `claude --plugin-dir <clone>`, `/flow:dev` on a throwaway repo whose `.flow.yml` holds three entries (a glob, a `{step: plan}`, a `{territory, name}`) reaches the upgrade's Asking message, which names the move, the two dropped entries and the key removal.
- `claude plugin validate .` passes; gate green.
- Read-through: `skills/dev/SKILL.md` and `agents/slice-runner.md` agree on the dispatch (packet, then the role's instruction, nothing between); README § Upgrading and `shared/work.md` § Upgrade say the same thing about `sources:`.

**Gotchas.** The README's § Upgrading and `shared/work.md` § Upgrade are the only places allowed to keep old names, by the gate's old-names check: `sources` isn't an old name in that check, so nothing to add there. `git mv` in the upgrade runs on the `flow-upgrade` branch, as the rest of the upgrade does.

---

## V3 — Guidelines on a territory, many documents

**In.** `shared/territories.md`:

- § Fields: `applies` added (a list of globs, or `always`), with its frontmatter form; `:173` ("the changelog, guidelines" stored with no frontmatter) now true of the scheda only.
- § Operations: `op: list` for `guidelines` returns one line per document of the folder, carrying name, `applies` and description (description last), an exception to `:194`'s status/work/touches; the scheda is not listed; a line with no description is skipped and named by the conductor. `op: read; name: <name>` returns one document; `op: read` with no `name` keeps returning the scheda, so the single-document rows for `guidelines` stay true. An adapter without guidelines support returns `unmet: op list on guidelines` (as `archive` does for `op archive`); `none` means a folder with no documents. A name may hold `/` (sub-folders): its scratch copy is `guidelines/<name>.md` with the `/` kept as folders, and `document` stays reserved for the scheda.
- § Reads: the rows for `guidelines` (opening or resume: `read` of the scheda plus `list`, then `read` by name of the `always` documents; impact and the slice packet: `list`, then `read` with `name` for the chosen documents; after the plan: `read` for a newly matched one). The existing § Reads › "Guidelines on a territory" paragraph (`:100`) is extended, not duplicated: `where` keeps meaning what the project declared and the adapter decides how the scheda and its siblings sit there; selection works from the `list` lines as from the frontmatter; the packet heading of a territory document is `### guidelines:<name>` (the rule V2 removed at `:108`, restated here for this kind).
- "One invocation per adapter per step" (`:47`, `:88`, `:126`) gains its exception: one more invocation for the `read`s a `list` decided, carrying `op: none` for the adapter's other declared kinds; the precedent is § Writes › Status change (`read`, then `write`), the only two-invocation moment today.
- § Resolution: an `unmet` on `op: list` for `guidelines` stops the run like any other unmet, with the message naming the adapter and the contract change. No fallback: the decision of 2026-11-03.
- § Writes: the `## Tests` write keeps targeting the scheda (`op: write` with no `name`), said where the write is described.
- `skills/dev/SKILL.md`: every place that reads guidelines names the adapter path for a declared kind (`list`, then `read`): the folder paragraph, the slice packet (`:296`), the review's re-match (`:308`, today only `decisions`), the always-reloaded resume row (`:190`), impact's packet (`:282`), § The criterion travels (`:272`) and the fidelity check (`:369`).

**Out.** Any adapter (none is Flow's). Mixed repo-and-territory folders (non-goal).

**Done.**

- `op: read`'s row in § Operations still names `guidelines` for a read with no `name`, and `op: list`'s row names `guidelines` among its kinds with its line format.
- Read-through of `shared/territories.md` alone, as its first paragraph demands ("someone who has read only it can write an adapter"): an adapter author can tell what `list` must return for `guidelines`, what `read` with and without `name` returns, what an old adapter must answer, and what Flow does on `none` and on `unmet`.
- The slice's "done on" narrative states, in one sentence, what an adapter must add (`op: list` for `guidelines` with the line format; `read` by name): `flow:docs` writes no operational instructions into the changelog and takes the entry from that narrative.
- `claude plugin validate .` passes; gate green.

**Gotchas.** The `list` and the `read`s of the chosen documents can't be one invocation (the names come from the list), hence the exception above; write it in all three places the rule is stated. The decision of 2026-11-03 ("stops on an old adapter") holds only if an old adapter answers `unmet`, not `none`: the `unmet: op list on guidelines` line is what makes the decision enforceable.

---

## V4 — This repo adopts the folder; the docs show it

**In.**

- `docs/development-guidelines.md` keeps `## Tests`, `## Gate`, `## References` (added, naming the brief, `docs/guidelines/releases.md` and `docs/guidelines/commits.md` by path, and the skills) and `## Deviations` (only if there is content); `## Naming`, `## Language`, `## Commits` and `## Releases` become files under `docs/guidelines/` (`naming.md`, `language.md`, `commits.md`, `releases.md`), each with a `description` and `applies: always` (the naming rules bind every document, the README and the site included; every merge is a release), text unchanged except relative links, which gain one `../`.
- Inbound references to the moved sections: `README.md:278` ("the commit rules: `docs/development-guidelines.md`") and the `development-guidelines.md#releases` anchors in `README.md` (§ Develop the plugin) and `docs/architecture.md:57` point at `docs/guidelines/releases.md` and `commits.md`.
- `skills/dev/impact-lenses.template.md`: two example lenses in its comments (multi-tenant ids; a DatoCMS sandbox that costs money), one line on the difference from a rule, and `:3` says coding rules go in the guidelines folder.
- `README.md`: the "Customizing a stack" section rewritten around the folder: where a stack's rules live (preset), where yours live (the folder, two frontmatter lines), one example with three Rails files, how to replace a preset. Short: Work 2 will cut the README further. Also `README.md:89` ("the guidelines that apply"), `:122` and `:208` ("naming and placement follow `docs/development-guidelines.md`") say the folder, and the project tree (`:164-189`) gains `guidelines/`. `docs/architecture.md`: `docs/guidelines/` row in the "What a project holds" table and node in its diagram; the scheda's row says what it holds now; `:133` ("the guidelines that apply") says the scheda and the folder. `site/index.html`'s project tree gains `guidelines/`.
- `docs/development-guidelines.md` § Gate: the link check's `rg --files` call gains `--glob '!docs/work/**/*-packet.md'` permanently (packets copy the scheda whole, so their relative links never resolve), with one sentence saying why; the narrowing V1 set for this work ends.
- `docs/CHANGELOG.md`: nothing here; the close writes it through `flow:docs`.

**Out.** The README's full rewrite and `docs/configuration.md` (Work 2). The site's text beyond the tree. `docs/design/flow_home-page.pen` (the site's design file holds the old tree; it follows in Work 2). The decision of 2026-10-25 (`projects-declare-marketplace-without-ref.md:28`) says "the guidelines' `## Releases` rule carries this": decisions aren't edited; known and accepted.

**Done.**

- `for f in docs/guidelines/*.md; do head -1 "$f" | grep -qx -- '---' && grep -q '^description:' "$f" || echo "bad: $f"; done` prints nothing.
- `rg -n '^## ' docs/development-guidelines.md` shows Tests, Gate, References and, at most, Deviations.
- Gate green from the repo root: validate, old names, links; the link check prints nothing for `docs/guidelines/`; `rg -n 'development-guidelines.md#' --glob '!docs/work/archive/**' .` shows no anchor to a moved section.
- `rg -n 'guidelines/' README.md docs/architecture.md site/index.html` hits each.
- With a clone of this repo in the scratchpad at this branch and `claude --plugin-dir <clone>`, `/flow:dev` on a throwaway repo that copies this repo's `docs/development-guidelines.md` and `docs/guidelines/` reaches a first stop whose guidelines line names `commits.md`, `language.md`, `naming.md` and `releases.md` with line counts.
- Read-through of the README section as the external developer of the brief: the three questions he asked (where the stack's guidelines live, where mine go, how I give several files) are answered in that order.

**Gotchas.** Until this release is installed, the conductor running this work is 0.5.0 and reads the scheda only: from V4's commit on, the close reads `docs/guidelines/releases.md` and `commits.md` by hand before the merge, with a note in `STATUS.md`'s Log. The site's tree is inside a `<pre>`: keep the indentation.
