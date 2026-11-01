# Slice packet — V1

## The criterion

- **Problem**: Flow reads and writes its documents only at fixed paths under `root:`, so a team whose decisions, product context or guidelines live in another tool (a wiki, a project board) can't point Flow at them, and the files its guidelines cite reach the agents only by chance.
- **Outcome**: a project declares, per kind of document, where it lives, through which adapter and with which writing peculiarities; Flow reads, writes and searches there and brings each agent the sources it needs. With nothing declared, everything stays under `docs/` as today.
- **Less about**: rewriting Flow's steps (review, gate, close); shipping adapters for specific services inside Flow; deciding for a project which document goes where; copying a territory's content into the repo.
- **Measure**: a document or change serves this work if it lets an agent or a teammate reach the right source without a human relaying it, leaves an unconfigured project behaving exactly as before, and puts no token or public share link in a repo.

## Slice V1 (from slices.md)

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

## Lessons

None yet (first slice).

## Test level

none

## Guidelines (docs/development-guidelines.md, sections that apply)

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

## Matching decisions

- docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md — Territory adapters run in the conductor's main session; flow:docs stays file-based. Rules out adapters behind flow:docs and a local mirror.
- docs/decisions-log/2026-10-29-main-session-grows-less-not-cleared.md — The /flow:dev conductor keeps context low by growing less, not by asking for /clear at every slice close.
- docs/decisions-log/2026-10-29-slice-execution-runs-in-a-runner-subagent.md — A slice's execution runs in one background subagent, flow:slice-runner; the main session keeps stops, STATUS.md and commits.
- docs/decisions-log/2026-10-26-work-runs-on-a-branch-in-the-current-checkout.md — A work runs on work/<slug> in the current checkout and closes with a local merge after an ok.

## Project lenses

none (no docs/impact-lenses.md)

## Preset rules

none (stack generic, no preset)

Repo root: /Users/spleenteo/Sites/me/flow
