# Slice packet — guidelines-folder, V1

Repo: /Users/spleenteo/Sites/me/flow, branch work/guidelines-folder. Stacks: [generic], no preset (no stack Rules). Effective test level: none. Project lenses: none (no docs/impact-lenses.md). No sources. No frame, so no criterion: the brief is the mandate.

Brief (the mandate; read Work 1): /Users/spleenteo/Sites/me/flow/docs/briefs/2026-11-03-guidelines-folder.md
Slices (this slice's section is below; the file holds V2–V4 for context on what is NOT this slice): /Users/spleenteo/Sites/me/flow/docs/work/2026-11-03-guidelines-folder/slices.md

## Shared gotchas (from slices.md)

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Other files carry their own strings: `shared/work.md:208` cites `§ Sources`; `agents/slice-runner.md` carries `Execute sources:` and `Close sources:`; `shared/territories.md` has "Sources naming a territory" and "a matched source names a territory". Renaming or removing one means fixing every citation: `rg -n -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' skills shared agents` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks. The link check strips anchors (`${t%%#*}`): a dead `#section` passes green, so anchors are checked by hand.
- `claude plugin validate .` passes with the warnings § Gate lists (missing version, missing icon, the folder also holding a marketplace manifest) plus OS-metadata warnings for files `git ls-files` doesn't print (`.DS_Store`); anything else is a finding.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).
- This session runs the installed plugin copy (0.5.0), not this clone. A Done item that needs the edited conductor to run uses a clone of this repo in the session scratchpad with `claude --plugin-dir <clone>` on a throwaway repo, never this checkout and never a branch created here (`docs/development-guidelines.md` § Tests and § Releases).


## The slice: V1 (from slices.md, verbatim)

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

---


## lessons.md

None yet (first slice).

## Guidelines (docs/development-guidelines.md), whole

# Development guidelines

Rules the plans and the changes in this repo must follow. The repo is a Claude Code plugin made of Markdown skills: there is no build and no test framework.

## Tests

Level: none
Sensitive areas:

No test framework. A slice is verified by running what it changed:

- `claude plugin validate .` for anything under `.claude-plugin/` or `skills/`;
- a throwaway test repo, created outside this repo (in the session scratchpad or `/tmp`), for loading, naming and settings behaviour. Never committed;
- for a change to a skill's instructions, a read-through against the brief and the skills that cite it.

Every behaviour of Claude Code a document states is either verified in a spike or a slice, or linked to the official docs page that says it.

## Naming

- A skill's folder is its `name:`, kebab-case: `skills/dev/SKILL.md` has `name: dev`.
- User-invocable skills are cited as `/flow:<skill>` in every document. Skills that aren't user-invocable (`flow:docs`, `flow:stack-<name>`, `flow:design-<tool>`) and agents (`flow:planner`, …) are cited as `flow:<name>`, with no slash. Dependency skills are cited by bare name, with the plugin form in brackets at the first mention: `shaping` (`shaping-skills:shaping`). The brackets go only at a dependency's first mention in each skill, bare afterwards.
- No names of specific user projects or personal tools anywhere. Examples use invented names.

## Language

English in every file, commit message and work document.

## Commits

Imperative mood, English, one concern per commit. Explicit `git add <paths>`, never `-A` or `.`. No `CLAUDE.md` at the repo root (`claude plugin validate` warns on it).

## Gate

Green before a slice closes, in order, from the repo root. The link check skips links inside fenced code blocks, where templates hold placeholder links. Both `rg` calls name the path (`.`): without one, `rg` reads stdin when it isn't a terminal and hangs.

```bash
claude plugin validate .        # passes; expected warnings: missing version and icon, the folder also holding a marketplace manifest
# old names: the only allowed matches are in shared/work.md § Upgrade and in the README's
# § Upgrading from an older Flow; nothing else.
# --hidden so .claude-plugin/ is searched too
rg -n -i --hidden '\bdevflow\b|devflow-(docs|archive)|projectflow|\.devflow\.yml' \
  --glob '!.git' --glob '!.git/**' \
  --glob '!docs/work/**' --glob '!docs/briefs/**' --glob '!docs/CHANGELOG.md' \
  --glob '!docs/development-guidelines.md' .
# every relative Markdown link resolves: prints "file -> target" per broken link, nothing when green.
# Add --glob '!path' to the rg --files call for the narrowing a work's slices.md sets.
rg --files --glob '*.md' --glob '!docs/work/archive/**' . | while read -r f; do
  awk '/^ *```/{x=!x; next} !x{s=$0; while(match(s,/\]\([^) ]+/)){print substr(s,RSTART+2,RLENGTH-2); s=substr(s,RSTART+RLENGTH)}}' "$f" |
    while read -r t; do
      case "$t" in http*|mailto:*|\#*) continue;; esac
      test -e "$(dirname "$f")/${t%%#*}" || echo "$f -> $t"
    done
done
```

While a work is in progress, its `slices.md` may narrow the Gate (for example, files a later slice rewrites); the narrowing ends with that work.

## Releases

How a change in this repo reaches the projects that use the plugin: see [Distribution](architecture.md#distribution).

- **Work on branches.** Every change, a work's documents included, is made on a branch.
- **A merge into `main` is a release.** Projects declare the `flow` marketplace without a `ref`, so whatever is on `main` is what they get. `main` never holds unreleased work: nothing is committed to it directly. Why: [projects declare the marketplace without a ref](decisions-log/2026-10-25-projects-declare-marketplace-without-ref.md).
- **Version and tag.** `/flow:changelog` bumps `.version` and writes the changelog entry on the branch, before the merge. After the merge, tag the merge commit `v` followed by the content of `.version` (`v0.4.0`) and push the tag.
- **Develop with `--plugin-dir`.** Run `claude --plugin-dir /path/to/clone` to load the clone you are editing for one session; it takes the place of an installed `flow` for that session ([flags that load a plugin for one session](https://code.claude.com/docs/en/plugins/cli-reference#flags-that-load-a-plugin-for-one-session)).
- **Never add the marketplace from the clone you are editing.** A marketplace added from a local path loads its relative-path plugins in place, from that folder, not from a copy in the cache ([in-place and copied plugins](https://code.claude.com/docs/en/plugins/loading#in-place-and-copied-plugins)). `flow` has the source `./`, so every session would run the conductor from the files it is editing: the conductor would edit itself. A user also has one marketplace per name ([marketplace reference](https://code.claude.com/docs/en/plugins/marketplace-reference)), so the local one would replace the released `flow`.


## Matching decisions (description lines)

- docs/decisions-log/2026-11-03-guidelines-are-a-folder-and-sources-goes-away.md — Guidelines are the files of <root>/guidelines/, chosen per slice by description; the sources: key goes away. Rules out a fourth mechanism.
- docs/decisions-log/2026-11-03-guidelines-territory-stops-on-an-old-adapter.md — A guidelines territory whose adapter answers op: list with unmet stops the run; Flow never degrades to the scheda alone.
- docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md — Territory adapters run in the conductor's main session; flow:docs stays file-based.
- docs/decisions-log/2026-11-01-scratch-folder-is-one-mktemp-outside-the-repo.md — The session scratch folder is one `mktemp -d` outside the repo.
- docs/decisions-log/2026-10-29-slice-execution-runs-in-a-runner-subagent.md — A slice's execution runs in one background subagent, flow:slice-runner.
- docs/decisions-log/2026-10-26-stack-presets-are-replaceable-skills.md — Stack knowledge ships as opinionated flow:stack-<name> skills; a project maps its own, replacing the shipped one entirely.
- docs/decisions-log/2026-10-27-design-runs-as-a-dev-phase.md — Inside /flow:dev, /flow:design is a tracked phase called by the conductor.
