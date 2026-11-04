# Slice packet — guidelines-folder, V3

Repo: /Users/spleenteo/Sites/me/flow, branch work/guidelines-folder. Stacks: [generic], no preset (no stack Rules). Effective test level: none. Project lenses: none (no docs/impact-lenses.md). No territories: key in this repo's .flow.yml (V3 edits the territory contract as text; nothing here runs an adapter). No guidelines folder in this repo yet (V4 creates it): the scheda below is the whole guidelines. No frame, so no criterion: the brief is the mandate. V2 removed `sources:` from the conductor, the work reference, the slice runner and the territory contract; line numbers cited in the slice below predate V2 and may have moved: locate by text.

Brief (the mandate; read Work 1): /Users/spleenteo/Sites/me/flow/docs/briefs/2026-11-03-guidelines-folder.md
Slices (this slice's section is below; the file holds V1–V2's narratives and V4 for context on what is NOT this slice): /Users/spleenteo/Sites/me/flow/docs/work/2026-11-03-guidelines-folder/slices.md

## Shared gotchas (from slices.md)

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Other files carry their own strings: `shared/work.md:208` cites `§ Sources`; `agents/slice-runner.md` carries `Execute sources:` and `Close sources:`; `shared/territories.md` has "Sources naming a territory" and "a matched source names a territory". Renaming or removing one means fixing every citation: `rg -n -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' skills shared agents` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks. The link check strips anchors (`${t%%#*}`): a dead `#section` passes green, so anchors are checked by hand.
- `claude plugin validate .` passes with the warnings § Gate lists (missing version, missing icon, the folder also holding a marketplace manifest) plus OS-metadata warnings for files `git ls-files` doesn't print (`.DS_Store`); anything else is a finding.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).
- This session runs the installed plugin copy (0.5.0), not this clone. A Done item that needs the edited conductor to run uses a clone of this repo in the session scratchpad with `claude --plugin-dir <clone>` on a throwaway repo, never this checkout and never a branch created here (`docs/development-guidelines.md` § Tests and § Releases).

**Gate narrowing for this work** (ruled at V1's close, 2026-11-04): the link check of § Gate runs with `--glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md'` added to its `rg --files` call. A packet copies `docs/development-guidelines.md` whole, so its relative links never resolve from the work folder. V4 makes `--glob '!docs/work/**/*-packet.md'` permanent in § Gate, and this narrowing ends with the work.


## The slice: V3 (from slices.md, verbatim)

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

## lessons.md

One rule per line, read by every later subagent.

## From V1

- When a packet starts carrying a whole file, run the link check on the packet too: relative links copied verbatim break from docs/work/<slug>/.
- An instruction that names a moment twice ("once, at impact and at the packet") is two rules: write each moment as its own clause.
- When a plan rewrites how one call's args are built, change the paragraph that defines the call and point to it from elsewhere; never restate the args in a second place.
- An executor whose Done says "agrees with § X" reads § X before reporting; a task-local wording check is not that check.
- On macOS, never edit with sed -i in an executor: it fails silently on BSD sed; use the Edit tool and re-read the changed line.

## From V2

- The link check doesn't skip inline code: never write a literal close-bracket-paren pair in prose or backticks; say "a Markdown link".
- A review fix that adds a rule of its own (a limit, an exception) gets checked against every place the same rule is stated (SKILL.md's frontmatter limit, the conversion paragraph, the README copy) before it passes.
- When one paragraph is copied verbatim into another file, every fix to the rule it summarises edits both copies in the same commit and compares the bytes.
- A fix-round instruction that rewords a phrase another file cites (T4's "line-only") names the citing file, so both change together.
- Shell in a skill (greps, awk) is run in a scratchpad throwaway on each edge case it names (comment in frontmatter, no frontmatter, unclosed block) before the review, not after.

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

- /Users/spleenteo/Sites/me/flow/docs/decisions-log/2026-11-03-guidelines-territory-stops-on-an-old-adapter.md — A guidelines territory whose adapter answers op: list with unmet stops the run; Flow never degrades to the scheda alone.
- /Users/spleenteo/Sites/me/flow/docs/decisions-log/2026-11-03-guidelines-are-a-folder-and-sources-goes-away.md — Guidelines are the files of <root>/guidelines/, chosen per slice by description; the sources: key goes away. Rules out a fourth mechanism.
- /Users/spleenteo/Sites/me/flow/docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md — Territory adapters run in the conductor's main session; flow:docs stays file-based. Rules out adapters behind flow:docs and a local mirror.
- /Users/spleenteo/Sites/me/flow/docs/decisions-log/2026-11-01-scratch-folder-is-one-mktemp-outside-the-repo.md — The session scratch folder is one `mktemp -d` outside the repo, one fresh subfolder per write; it rules out folders in the git dir or the work tree.
- /Users/spleenteo/Sites/me/flow/docs/decisions-log/2026-10-29-slice-execution-runs-in-a-runner-subagent.md — A slice's execution runs in one background subagent, flow:slice-runner; the main session keeps stops, STATUS.md and commits.
