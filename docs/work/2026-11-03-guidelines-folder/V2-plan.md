---
work: guidelines-folder
slice: V2
updated: 2026-11-04
tags: [work, guidelines-folder, plan]
description: "Plan of V2: sources: leaves the conductor, the runner, the contract and the docs; § Upgrade converts it into guidelines-folder files"
---

# V2 — `sources:` goes away, with its upgrade — Plan

**Mandate**: [slices.md](slices.md) § V2, from Work 1 of [the brief](../../briefs/2026-11-03-guidelines-folder.md) ("`sources:` goes away", "Upgrade") · **Guidelines**: [development-guidelines.md](../../development-guidelines.md) · **Lessons**: [lessons.md](lessons.md) · **Execution rules**: [executor.md](../../../agents/executor.md) (Test policy, level `none`)

**Summary**
1. T1 (critical) removes § Sources from `skills/dev/SKILL.md`, moves its glob rule into § Guidelines folder and removes every citation of it.
2. T2 (critical) gives `shared/work.md` § Upgrade the `sources:` case: one check line, the reads at Asking, the Rewritten rows, the pointed-at lines, the commit pathspec, Resume (b).
3. T3 drops the step's sources from the dispatch contract and from `agents/slice-runner.md`. T4 says in `SKILL.md` what the upgrade may read before § Territories.
4. T5 (critical) removes the sources rows and paragraph from `shared/territories.md`. T6 updates the template and `docs/architecture.md`. T7 updates the README.
5. T1, T2, T5 and T6 run first, side by side. T3 and T7 follow T2; T4 follows T1 and T2. The gate and the Done's runs happen once, at the close.

**Files**: skills/dev/SKILL.md, shared/work.md, agents/slice-runner.md, shared/territories.md, skills/dev/development-guidelines.template.md, docs/architecture.md, README.md

## The slice, decided here
- **Current lines** (after V1). `SKILL.md`: config sentence :50, folder paragraph :52–58, § Sources :60–72, Dependencies rule :114 (slices' `:104`), Upgrade check :126 (`:116`), resume rows :209–210, criterion travels :282, impact :292, packet :306, planner :310, review :318, first stop :328, packet place :338, runner fields :339, loop :354/:356/:359 (`:346`/`:349`), gate fix round :365. `shared/work.md`: dispatch contract :208, § Upgrade :385–431 (`:387` unchanged). `shared/territories.md`: :81, :83, :102–108 and :112, all unchanged. Template comment: :9 (slices' `:7`).
- **Glob rule.** :67's text moves, verbatim, as a new folder bullet right after Listing, with the quoting reason from :66 in front: "Globs are quoted, because a flow-style value starting with `*` is a YAML alias." Listing's "in the glob syntax of § Sources" becomes "in the glob syntax below". :55's "as § Sources matches" becomes "by the glob rule above". :56 loses its `## Sources` sentence. The first stop keeps only the `guidelines in packet:` line.
- **The conversion sentence**, written by T2 and copied verbatim by T7: "A `sources:` key in `.flow.yml` is converted: a `{territory, name}` entry is dropped and named, whatever its `when` holds; a `path` entry with at least one glob becomes a file of `<root>/guidelines/`, moved there with `git mv` and given a frontmatter of `applies` (its globs, merged across entries sharing a path) and a `description` drafted from its first heading, for the user to rewrite; when the destination `<root>/guidelines/<basename>` is already taken, the file gets its frontmatter where it is and is named, not moved; a `path` entry with only `{step}` or `{phase}` values and a missing path are dropped and named, and their files stay where they are; the key is removed. References to a moved file, its relative links that break and the guidelines file's sections beyond its four are pointed at, not edited."
- **Drafted description**: `description: "<first heading, without its #s; \" becomes '>"  # drafted by the upgrade: rewrite it`. With no heading, the basename without `.md`. Keys the file already has (read at Asking, below) are kept and not drafted again; the first heading is looked for after that block, so a YAML comment never counts. An existing `applies` is kept, and the entry's globs are named in the message.
- **Collision**: the exact destination `<root>/guidelines/<basename>` already exists on the default branch, or two entries' different paths share the basename. The frontmatter is written, the file stays, and the message names it with the `git mv` for the user. A path already under `<root>/guidelines/` gets the frontmatter, no move.
- **Asking reads**, all line-only, only when the check printed `sources:`, in one call that first recomputes `r` with the check block's first line (shell state doesn't carry between calls): `git show <default>:.flow.yml`; per path, `git cat-file -e <default>:<path>`, its frontmatter block only (V1's frontmatter awk on `git show <default>:<path>`, printing the block's lines and nothing after it), and the first `^#` line after that block; inbound references, `git grep -n -F '<basename>' <default> -- '*.md' ':!<path>' ":!$r/work/**"`, keeping lines where the name sits in an `@` import or a Markdown link; outbound links, `git grep -n -o -E '\]\([^)]+\)' <default> -- <path>`, each relative one resolved from the new folder and named when `git cat-file -e <default>:<resolved>` fails; extra sections, `git grep -n '^## ' <default> -- "$r/development-guidelines.md"`, beyond References, Tests, Gate, Deviations. When `.flow.yml` also declares `guidelines` under `territories:` (seen in the `git show` above), the message says the moved files won't be read while that territory stands.
- The README's "Add rules of your own" row (:122, "the sections that apply") stays: V4 rewrites the section. The README links its folder example to `skills/dev/SKILL.md` with no anchor, so the link check covers it.

## Tasks

### T1 — `skills/dev/SKILL.md` without § Sources — critical
**What**: :50 loses "`sources` (a flow list of `{path, when}`; see § Sources below), ". The folder paragraph changes as described under Glob rule. :60–72 are deleted. :209 becomes "…the decisions matched by the slice's `files:` and the `docs/guidelines/` files chosen for the slice (§ Guidelines folder); lenses and decisions come through the adapter when declared". :210 "re-matches the decisions and the `applies` globs of `docs/guidelines/`". :282 loses ", then the `phase: <name>` sources when one is declared for that phase (§ Sources)". :292 loses its `phase: impact` sentence. :306 loses "When `.flow.yml` has `sources:`, … (§ Sources);" and keeps "a declared kind's guidelines, decisions and lenses come from its adapter". :310 loses the `## Sources for step: plan` clause. :318 loses its "With `sources:` …" sentence. :328 loses its `sources in packet` sentence. :338 loses ", or a matched source names a territory". :339 loses the two sources fields and their bracket. :354 becomes "packet verbatim on top, the task text, …". :356 and :359 lose "the `step: execute` block, ". :365's fix round becomes "(a fresh executor with the failure, then a review)".
**Files**: skills/dev/SKILL.md
**Interfaces**: produces § Guidelines folder as the only home of the glob rule. T4 edits the same file.
**Verified**: ``rg -n -i '§ Sources|## Sources|Execute sources|Close sources|sources in packet|matched source|sources:|`sources`|step: (execute|close|plan|review)` block' skills/dev/SKILL.md`` prints nothing. `rg -n -i '\bsources\b' skills/dev/SKILL.md` prints the "When sources disagree" and "sources, transcripts" lines only. `rg -n -F 'YAML alias' skills/dev/SKILL.md` prints one line between `**Guidelines folder.**` and `**Territories.**`. `rg -c -F 'guidelines in packet' skills/dev/SKILL.md` prints `2`.
**Done**: the dispatch reads "packet, then the role's instruction" at every move, and the folder paragraph holds the whole glob rule.

### T2 — § Upgrade converts `sources:` — critical
**What**: in `shared/work.md`: :387 becomes "The upgrade rewrites the ones Flow owns, and the files `sources:` names, and points at the ones it doesn't." The block gains `git grep -n '^sources:' <default> -- .flow.yml` after the `ls-tree` line, and its loop becomes `do { git cat-file -e "$b:.devflow.yml" 2>/dev/null || git grep -q '^sources:' "$b" -- .flow.yml; } && echo "$b"; done`. After :404 add: "Of `sources:`, only the key line is a trace; the reads and lines below happen only in an upgrade it triggered." Before the table, a paragraph `` **`sources:`** (repo only: `/flow:project` has none) `` holding the conversion sentence. Rows, in this order: `{territory, name}`: dropped and named whatever its `when` holds, since the kind lists its own documents; a `path` entry with a glob (frontmatter merged into any existing block, the description draft, the move and the collision as decided above); a `path` entry with no glob left: dropped and named, the file stays; a path missing on the default branch: dropped and named; the key: removed with its block-style lines. Pointed-at gets three bullets: references, broken links and extra sections, each as `<file>:<line> — <what>`. Asking gets the reads above and lists each move, drop and the key removal as `file — what changes`. Writing, repo: the `git mv` runs on `flow-upgrade`, after the ok, and the file is read there whole to merge the frontmatter at the new path, and `git commit -m … -- <paths>` names the source and the destination of every move. Resume: the first sentence adds `git grep -n '^sources:' work/<slug> -- .flow.yml`, and (b) adds "and the `sources:` rows of the table".
**Files**: shared/work.md
**Interfaces**: produces the conversion sentence and the name "§ Upgrade › Asking" for T4 and T7.
**Verified**: copy the block to the scratchpad with `<default>` set to `main` and run it from this repo's root: it prints `upgrade check: done` alone. Repeat in `git init` throwaways in the scratchpad: a committed `.flow.yml` of `sources: [{path: a.md, when: ["x/**"]}]` prints `main:.flow.yml:1:sources: …` (a revision's `git grep -n` prefixes it); a `docs/development-guidelines.md` with `## Naming` and no `sources:` prints `upgrade check: done` alone. `rg -n -i '\bsources\b' shared/work.md` prints :208 and lines past `## Upgrade` only.
**Done**: every bullet of the slice's `shared/work.md` item has a sentence in § Upgrade.

### T3 — The dispatch carries no sources (depends on: T1, T2)
**What**: `shared/work.md:208` becomes "The prompt is the slice packet (or the conductor's own packet) verbatim at the top, then the role's instruction." In `agents/slice-runner.md`, :22 is deleted, and :29 ends at "…then the role's instruction." with no sources sentences.
**Files**: shared/work.md, agents/slice-runner.md
**Verified**: `rg -n -i 'sources' agents/slice-runner.md` prints nothing. ``rg -n -F "then the role's instruction" shared/work.md agents/slice-runner.md`` prints one line in each.
**Done**: the runner and the contract agree with `SKILL.md`'s dispatch moves; the executor reads, after T1, Step 4's `step: execute` move 4 (the runner's fields) and the loop's executor bullet, found by text, before reporting.

### T4 — What the upgrade may read (depends on: T1, T2)
**What**: in `skills/dev/SKILL.md` (lines found by text: they move after T1), § Dependencies' bold sentence ("Until § Territories resolves, no `docs/` file is read…") adds to its allowed list "and, when the check found the old configuration key, the line-only reads of the work reference, § Upgrade › Asking". § Upgrade check adds one sentence that only points: "The reads it may make before § Territories are those of the work reference, § Upgrade › Asking." Neither sentence uses the word the Done's `rg` looks for.
**Files**: skills/dev/SKILL.md
**Verified**: `rg -c -F '§ Upgrade › Asking' skills/dev/SKILL.md` prints `2`. T1's `rg -n -i '\bsources\b' skills/dev/SKILL.md` still prints only "When sources disagree" and "sources, transcripts". Read T2's Asking paragraph: the reads it lists are the ones these two sentences allow.
**Done**: no read that § Upgrade makes falls outside § Dependencies' allowed list.

### T5 — The contract drops sources — critical
**What**: in `shared/territories.md`, delete rows :81 and :83 and the paragraph :102–108 (yaml included). In :112, drop ", or a matched source names a territory".
**Files**: shared/territories.md
**Verified**: `rg -n -i '\bsources?\b' shared/territories.md` prints nothing. `rg -c '^\| Impact; slice packet; resume at' shared/territories.md` prints `2`.
**Done**: the § Reads table and Packet place name only kinds.

### T6 — The template and the architecture page
**What**: the template's :9 sentence becomes "A document of your own that Flow's agents must read goes in the `guidelines/` folder next to this file, with a `description` saying when it matters." In `docs/architecture.md`, :135 loses ", the matching sources" and its "Sources scoped to a step…" sentence, and :198 loses ", `sources`".
**Files**: skills/dev/development-guidelines.template.md, docs/architecture.md
**Verified**: `rg -n -i '\bsources?\b' skills/dev/development-guidelines.template.md` prints nothing. `rg -c -F 'guidelines/' skills/dev/development-guidelines.template.md` prints `3`. `rg -n -i '\bsources\b' docs/architecture.md` prints the "plugin sources" and "HTML sources" lines only.
**Done**: neither file names the key.

### T7 — The README stops describing `sources:` (depends on: T2)
**What**: in :116, "point Flow at them (below)" becomes "put them in `docs/guidelines/` (below)". Row :124 becomes `| Give agents several documents, each for the slices that need it | Put them in `docs/guidelines/`, each opening with a `description` and, optionally, `applies` | A file reaches a slice's agents when its globs match the slice's paths, when its description fits the slice, or always (`applies: always`); the first stop names the files chosen |`. § Several files (:128–140) shows `docs/guidelines/rails/testing.md`'s frontmatter in a fenced block (`description`, `applies: ["spec/**", "test/**"]`), says `naming.md` takes `applies: ["app/**"]` and `deploy.md` a description only, and keeps "keep those files short": each chosen file is read whole on every dispatch. It ends with "The rules: `skills/dev/SKILL.md`, § Guidelines folder." with the first part as a link to that file. In § Configuration, the `sources:` example line (:231) and the paragraph (:236) are deleted. Item 4 (:261) gains T2's conversion sentence, verbatim, after the sentence ending "…that it proposes to merge.", never inside a sentence.
**Files**: README.md
**Verified**: ``rg -n -i '\bsources\b|`when`' README.md`` prints item 4 of § Upgrading only. `rg -n -F 'docs/guidelines/' README.md` prints :116, the table row and § Several files. `rg -c -F 'its relative links that break and the guidelines file' README.md shared/work.md` prints `1` for each file, and the two sentences read the same word for word.
**Done**: no README sentence states a `sources:` behaviour outside § Upgrading.

## Order
T1, T2, T5 and T6 run in parallel: their files are disjoint. T7 runs after T2; T3 and T4 run after T1 and T2. At the close, the gate runs with this work's packet narrowing, then the Done's `rg` with the filter of Deviation 1, then the Done's `--plugin-dir` run (likely `pending:`, with the user), then the read-through of both Done pairs.

## Deviations from the mandate
- Done item 1's `\bsources?\b` also matches about 25 unrelated singular uses (`<source media`, the marketplace's `"source"`, "source material", "source of truth", "relative-path source", "source tree", "HTML source", "never the source", "the source for the", "source ./", "the source `./`", "source changes", "its `source`", "Its source is"; the four last ones cover `docs/architecture.md:24` and `:51`, `README.md:57` and `skills/design/SKILL.md:93`). The close pipes the Done's `rg` through `rg -v -i` on those phrases and expects only the allowed set and the two upgrade sections.
- "Both reads happen after the check and only on the ok" is read as "only when the check found `sources:`". They run at Asking, before the user's ok, because the Asking message must name each move (Done item 3).
- Asking also runs line-only `git grep`s for inbound references, broken links and extra sections, which the Pointed-at list needs. `SKILL.md` :114/:126 allow them by naming § Upgrade › Asking, not just the mandate's two reads.
