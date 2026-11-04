# Slice packet — guidelines-folder, V2

Repo: /Users/spleenteo/Sites/me/flow, branch work/guidelines-folder. Stacks: [generic], no preset (no stack Rules). Effective test level: none. Project lenses: none (no docs/impact-lenses.md). No sources: key in .flow.yml. No guidelines folder in this repo yet (V4 creates it): the scheda below is the whole guidelines. No frame, so no criterion: the brief is the mandate.

Brief (the mandate; read Work 1): /Users/spleenteo/Sites/me/flow/docs/briefs/2026-11-03-guidelines-folder.md
Slices (this slice's section is below; the file holds V1's narrative and V3–V4 for context on what is NOT this slice): /Users/spleenteo/Sites/me/flow/docs/work/2026-11-03-guidelines-folder/slices.md

## Shared gotchas (from slices.md)

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Other files carry their own strings: `shared/work.md:208` cites `§ Sources`; `agents/slice-runner.md` carries `Execute sources:` and `Close sources:`; `shared/territories.md` has "Sources naming a territory" and "a matched source names a territory". Renaming or removing one means fixing every citation: `rg -n -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' skills shared agents` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks. The link check strips anchors (`${t%%#*}`): a dead `#section` passes green, so anchors are checked by hand.
- `claude plugin validate .` passes with the warnings § Gate lists (missing version, missing icon, the folder also holding a marketplace manifest) plus OS-metadata warnings for files `git ls-files` doesn't print (`.DS_Store`); anything else is a finding.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).
- This session runs the installed plugin copy (0.5.0), not this clone. A Done item that needs the edited conductor to run uses a clone of this repo in the session scratchpad with `claude --plugin-dir <clone>` on a throwaway repo, never this checkout and never a branch created here (`docs/development-guidelines.md` § Tests and § Releases).

**Gate narrowing for this work** (ruled at V1's close, 2026-11-04): the link check of § Gate runs with `--glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md'` added to its `rg --files` call. A packet copies `docs/development-guidelines.md` whole, so its relative links never resolve from the work folder. V4 makes `--glob '!docs/work/**/*-packet.md'` permanent in § Gate, and this narrowing ends with the work.


## The slice: V2 (from slices.md, verbatim)

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


## lessons.md

# Lessons — guidelines-folder

One rule per line, read by every later subagent.

## From V1

- When a packet starts carrying a whole file, run the link check on the packet too: relative links copied verbatim break from docs/work/<slug>/.
- An instruction that names a moment twice ("once, at impact and at the packet") is two rules: write each moment as its own clause.
- When a plan rewrites how one call's args are built, change the paragraph that defines the call and point to it from elsewhere; never restate the args in a second place.
- An executor whose Done says "agrees with § X" reads § X before reporting; a task-local wording check is not that check.
- On macOS, never edit with sed -i in an executor: it fails silently on BSD sed; use the Edit tool and re-read the changed line.


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
- docs/decisions-log/2026-10-29-slice-execution-runs-in-a-runner-subagent.md — A slice's execution runs in one background subagent, flow:slice-runner; the main session keeps stops, STATUS.md and commits. No /clear, no /compact.
- docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md — Territory adapters run in the conductor's main session; flow:docs stays file-based. Rules out adapters behind flow:docs and a local mirror.
- docs/decisions-log/2026-11-01-scratch-folder-is-one-mktemp-outside-the-repo.md — The session scratch folder is one `mktemp -d` outside the repo, one fresh subfolder per write; it rules out folders in the git dir or the work tree.
- docs/decisions-log/2026-10-26-work-runs-on-a-branch-in-the-current-checkout.md — A work runs on work/<slug> in the current checkout and closes with a local merge after an ok; no worktree unless asked, a PR only on request or by rule.
- docs/decisions-log/2026-10-26-atomic-commit-criteria-absorbed-not-a-dependency.md — One commit per task under atomic-commits' criteria, written once in Flow's executor prompt; the plugin is not a dependency and superpowers' commit default goes.
