---
work: guidelines-folder
slice: V1
updated: 2026-11-04
tags: [work, guidelines-folder, plan]
description: "Plan of V1: the conductor learns docs/guidelines/ beside sources:, the guidelines file travels whole, the template and two shared files follow"
---

# V1 — The folder in the conductor — Plan

**Mandate**: [slices.md](slices.md) § V1, from Work 1 of [the brief](../../briefs/2026-11-03-guidelines-folder.md) · **Guidelines**: [development-guidelines.md](../../development-guidelines.md) · **Lessons**: none yet (first slice) · **Execution rules**: [executor.md](../../../agents/executor.md) (Test policy, level `none`)

**Summary**
1. T1 (critical) writes the `**Guidelines folder.**` paragraph in `skills/dev/SKILL.md` and, in the same commit, rewrites every sentence that reads the guidelines: the file travels whole, the folder adds its share. `sources:` and § Sources stay.
2. T2 updates Profile › The draft and Step 0 in the same file.
3. T3 updates the guidelines template. T4 updates `shared/work.md:138` and `shared/territories.md:23`.
4. T1 runs first. T2, T3 and T4 then run side by side, since each edits other files. The gate runs once, at the close.

**Files**: skills/dev/SKILL.md, skills/dev/development-guidelines.template.md, shared/work.md, shared/territories.md

## The slice, decided here
- Line numbers are at `d65cad7`. The skill text never says "scheda", because the guidelines require English. It says `docs/development-guidelines.md`, or "the guidelines file", and uses `docs/guidelines/` literally (the default root, as the rest of the skill does). Each edit the Done's `rg 'guidelines/'` checks contains that string.
- **Placement.** A bold paragraph `**Guidelines folder.**` goes between the configuration paragraph (:49) and `**Sources.**` (:51). Other text cites it as "§ Guidelines folder". For the glob syntax and the directory-prefix rule it cites "§ Sources", and does not copy them; V2 moves them in. `.flow.yml` gains no key, so :49 stays as it is.
- **Listing.** The folder is read from the checkout, after § Territories: every `*.md` at any depth, from `rg --files docs/guidelines -g '*.md'`. Sub-folders carry no meaning. Selection reads only the frontmatter: the lines between a first-line `---` and the next `---`, printed by one `awk` over the listed files that moves to the next file at the closing `---`, at a first line that isn't `---`, or after ten lines (`awk 'FNR==1&&$0!="---"{nextfile} FNR>1&&$0=="---"{nextfile} FNR>10{nextfile} FNR>1{print FILENAME": "$0}' <files>`); a file's text is read only once chosen, when the packet is built. A file is skipped, and named in the next message to the user, when it has no such block, no `description:`, or an `applies` that is neither a flow list of quoted globs nor `always`. A skip never stops a step. There is no `git ls-files` check, because a first run may hold files that are not committed yet.
- **Selection.** `applies: always` → in. Globs → in when they match the paths the slice's section of `slices.md` names, as § Sources matches. No `applies` → in when the conductor judges the `description` relevant to the slice's section, from the description alone; it never opens the file to decide. At impact the paths and the text are those of `slices.md` in full. After the plan, only the globs are matched again, against the planner's `Files:`. New files are appended to `## Guidelines` before the review dispatch. Judgment is not re-run then: the brief ties it to the slice's section.
- **Packet form.** The guidelines file comes whole. Then comes `## Guidelines`: one `### <repo-relative path>` per chosen file, sorted by path, its text verbatim. `## Sources` keeps its place (§ Sources). A path already under `## Guidelines` is not repeated under `## Sources`. When no file is chosen, the packet has no `## Guidelines` heading.
- **First stop.** The stop message gets one line: `guidelines in packet: docs/guidelines/testing.md (42 lines), …`, or `guidelines in packet: none`. It appears whenever the folder exists and `guidelines` is not on a territory; with no folder there is no line. The files left out are not named. The `sources in packet` line stays beside it (V2 drops it).
- **Moments without slice paths** get the guidelines file whole plus the `applies: always` files, with no judgment. They are the opening, the resume's always-reloaded row, the criterion block of § The criterion travels (`shaping`, `framing-doc`, `breadboarding`, `/flow:design`) and the fidelity check of Step 5 move 1.
- **Territory.** With `guidelines` declared, the repo folder is neither listed nor read, and no message names it; the Conventions row and the folder paragraph state the condition, nothing else mentions it. The guidelines file comes from the adapter as today; V3 adds the adapter path.
- :104 is not edited: "no `docs/` file is read" until § Territories resolves already covers the folder. :40 (what the skill writes) is not edited either, because the skill never writes the folder.
- Template: :3 stays word for word (`shared/work.md:411`'s upgrade row targets that opening), and so does :7's `sources:` comment (V2 changes it). The new note is an HTML comment like the template's others, with no links, so it never trips the link check.

## Tasks

### T1 — The folder paragraph, and the guidelines file whole everywhere it is read — critical
**What**: in `skills/dev/SKILL.md`, one commit:
- **Folder paragraph (new, after :49).** Holds the folder's role and the two keys, plus every item under Listing, Selection, Packet form, First stop, Moments without slice paths and Territory above.
- **:28.** The "Pass whole documents to subagents" row's Why gains ", except `docs/development-guidelines.md` and the chosen `docs/guidelines/` files, which travel whole".
- **:42 and a new row.** :42 becomes "Read if present, after § Territories; it goes whole into every context packet", and the template note stays. Below it, a new row: `| Guidelines folder | docs/guidelines/ | Read if present, after § Territories, unless guidelines is on a territory: each file's frontmatter (description, applies); the files chosen per slice go into its packet (§ Guidelines folder) |`.
- **:59.** "after the guideline sections" becomes "after the guidelines file and its `## Guidelines` section (§ Guidelines folder)".
- **:76 and a new row.** :76's row holds the test level and areas, the gate, the references and the deviations; read whole, at the same moments. A new row follows: `docs/guidelines/`, "How code is written here, one file per concern", read "its frontmatter when a packet is built; the chosen files in every plan, review, execution and close".
- **:90.** "the guideline sections that apply to it" becomes "the guidelines file whole and the `docs/guidelines/` files chosen for it".
- **:190.** "the sections of `docs/development-guidelines.md` that apply" becomes "`docs/development-guidelines.md` whole and the `applies: always` files of `docs/guidelines/`".
- **:199.** Add "the `docs/guidelines/` files chosen for the slice (§ Guidelines folder)" next to the sources.
- **:200.** "re-matches the decisions, the sources and the `applies` globs of `docs/guidelines/`".
- **:272.** "with the guideline sections that bear on it" becomes "with `docs/development-guidelines.md` whole and the `applies: always` files of `docs/guidelines/`", and the `/flow:design` sentence says its args carry them too, after the `Shaping:` line.
- **:282.** "the guidelines" becomes "`docs/development-guidelines.md` whole, then the `## Guidelines` section of the `docs/guidelines/` files chosen against `slices.md` in full (§ Guidelines folder)".
- **:296.** "the sections of `docs/development-guidelines.md` that apply (tests, naming, placement, gate)" becomes "`docs/development-guidelines.md` whole, then the `## Guidelines` section of the `docs/guidelines/` files chosen for the slice (§ Guidelines folder)".
- **:308.** Add: "Match the `applies` globs of `docs/guidelines/` again the same way, and append what is new to `## Guidelines`".
- **:318.** Add: "When the folder is read, also the `guidelines in packet:` line (§ Guidelines folder)".
- **:369.** "the applicable guidelines" becomes "`docs/development-guidelines.md` whole, the `applies: always` files of `docs/guidelines/`".
**Files**: skills/dev/SKILL.md
**Interfaces**: produces the paragraph name `§ Guidelines folder`, the heading `## Guidelines`, the line `guidelines in packet:` and the term "the guidelines file". T2, T3 and T4 use those words verbatim.
**Verified**, from the repo root:
- `rg -n -i 'sections that apply|relevant sections|guideline sections|applicable guidelines|bear on it' skills/dev/SKILL.md` prints nothing.
- `rg -n -F '**Guidelines folder.**' skills/dev/SKILL.md` prints one line, between the `.flow.yml` paragraph and `**Sources.**`.
- `rg -n 'guidelines/' skills/dev/SKILL.md` prints lines in the Conventions table, the folder paragraph, Project memory, cost rule 1, the resume table (`plan` and `review` rows), impact, the slice packet and `step: review`.
- `rg -n -F 'guidelines in packet' skills/dev/SKILL.md` prints the folder paragraph and the first stop. `rg -c -F 'sources in packet: none' skills/dev/SKILL.md` prints `1`.
- `rg -c -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' shared agents` prints `shared/territories.md:4`, `shared/work.md:1` and `agents/slice-runner.md:2`, unchanged.
- `claude plugin validate .` passes with only the expected warnings of § Gate (plus `.DS_Store`).
**Done**: every sentence of the brief's "The folder", "The scheda stays" (except the draft and the template), "Selection" and "Moments without slice paths" has a place in the paragraph or at its moment, and no two paragraphs give a different rule for the same moment.

### T2 — The draft and Step 0 name the folder (depends on: T1)
**What**: in `skills/dev/SKILL.md`:
- **:145.** Drop "the code's observed conventions where the presets are silent;". Add after the sections list: "only those four sections; prose about naming, placement and tests goes in `docs/guidelines/` files (§ Guidelines folder), never in the draft".
- **:164.** Add `docs/guidelines` to the `ls -d` line.
- **:169.** After the collision clause, add: "when `guidelines` is not on a territory, an existing `docs/guidelines/` (a docs site may have one) is reported: its files with a `description` will be read as guidelines; it is never moved or edited. With a territory, the repo folder is ignored and not named".
- **:174.** Add one sentence, under the same "not on a territory" condition: "Split guidance goes in `docs/guidelines/`, one Markdown file per concern, each opening with a frontmatter of `description` (required, one line) and `applies` (optional: globs, or `always`)".
**Files**: skills/dev/SKILL.md
**Verified**:
- `rg -n -c 'observed conventions' skills/dev/SKILL.md` prints nothing.
- `rg -n 'docs/guidelines' skills/dev/SKILL.md | wc -l` prints T1's count for the same command plus 4; the four new hits are The draft (:145), the `ls -d` line (:164), the `docs/ exists` paragraph (:169) and the offers paragraph (:174). No `claude plugin validate` here: T3 edits a skill file in parallel; the gate runs it at the close.
**Done**: the brief's "First run" and the draft sentence of "The scheda stays" each have a place. Nothing in Step 0 detects or moves files.

### T3 — The template sends prose to the folder (depends on: T1)
**What**: in `skills/dev/development-guidelines.template.md`, add an HTML comment after :3: "Prose about naming, placement and how tests are written goes in files of the `guidelines/` folder next to this file, each opening with a frontmatter of `description` and, optionally, `applies` (globs, or `always`). This file keeps its four sections." Change the :14 comment to: "Framework and command only. How tests are written goes in a `guidelines/` file." :3 and :7 stay as they are.
**Files**: skills/dev/development-guidelines.template.md
**Verified**:
- `rg -n 'guidelines/' skills/dev/development-guidelines.template.md` prints two lines: the note and the Tests comment.
- `rg -n -i 'good test|arrange/act' skills/dev/development-guidelines.template.md` prints nothing.
- `rg -c '^## ' skills/dev/development-guidelines.template.md` prints `4`.
- `rg -c -F 'passes this file' skills/dev/development-guidelines.template.md` prints `1`.
**Done**: the template invites no prose beyond the four sections.

### T4 — Shared files name the folder (depends on: T1)
**What**: in `shared/work.md:138`, "(repo: `development-guidelines.md`; …)" becomes "(repo: the files of `<root>/guidelines/`; …)", with the project part unchanged. In `shared/territories.md:23`, the undeclared `guidelines` row becomes "`<root>/development-guidelines.md` and the files of `<root>/guidelines/`". Nothing else in the contract changes (V3).
**Files**: shared/work.md, shared/territories.md
**Verified**:
- `rg -n -F '<root>/guidelines/' shared/work.md shared/territories.md` prints `shared/work.md:138` and `shared/territories.md:23` (`shared/territories.md:136` matches the bare `guidelines/` already today and is not this task's).
- ``rg -n -F 'repo: `development-guidelines.md`' shared/work.md`` prints nothing.
**Done**: both files agree with § Guidelines folder.

## Order
T1 runs alone first: the other three cite its wording, and T2 edits the same file. T2, T3 and T4 then run in parallel, since their files are disjoint. The gate (validate, old names, links), the Done's read-through of the whole diff against the brief, and a hand check of any new `#anchor` all run once, at the close.

## Deviations from the mandate
- `skills/dev/SKILL.md:59` (§ Sources) is edited although the slice's list omits it. Its "after the guideline sections" would fail the Done's `rg 'guideline sections'`, and the packet order changes there.
