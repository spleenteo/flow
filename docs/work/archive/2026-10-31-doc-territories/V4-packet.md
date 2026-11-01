# Slice packet — V4

## The criterion

- **Problem**: Flow reads and writes its documents only at fixed paths under `root:`, so a team whose decisions, product context or guidelines live in another tool (a wiki, a project board) can't point Flow at them, and the files its guidelines cite reach the agents only by chance.
- **Outcome**: a project declares, per kind of document, where it lives, through which adapter and with which writing peculiarities; Flow reads, writes and searches there and brings each agent the sources it needs. With nothing declared, everything stays under `docs/` as today.
- **Less about**: rewriting Flow's steps (review, gate, close); shipping adapters for specific services inside Flow; deciding for a project which document goes where; copying a territory's content into the repo.
- **Measure**: a document or change serves this work if it lets an agent or a teammate reach the right source without a human relaying it, leaves an unconfigured project behaving exactly as before, and puts no token or public share link in a repo.

## Slice V4 (from slices.md)

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

## Lessons

Rules learned while executing, one per line. Every plan and packet carries them.

- In `claude -p`, `--allowedTools "<list>"` is variadic and swallows a prompt argument after it; pipe the prompt on stdin.
- A Verified line that forbids a literal (`rg -n '\*-<slug>'` → no output) also forbids quoting it in prose; plan the check to match only the command, or word the prose without it.
- A slug lookup by substring (`rg "<slug>"`, `*-<slug>`) matches longer slugs; anchor it on the exact field (`^work: <slug>$`, or the backticked slug with `-F`).
- A skill's glob over `<root>/work/*/` gets `2>/dev/null`, as `shared/work.md` does, so an empty folder reads as no match.
- In `claude -p`, a check that asks the model to print the prompts it would build is flagged by the safety classifier (`reasoning_extraction`); ask instead for a routing table of which files each dispatch carries, and grep that.
- A file whose text reaches a committed document (a packet, a ledger) must be tracked by git (`git ls-files --error-unmatch <path>`), or an ignored local secret lands in the repo.
- When the plan's example and the one the first task writes differ, rule on one form at the first review and hand it word for word to every later task that copies it.
- A skill's ordering rule ("read only X until Y") placed in a reference paragraph does not hold headless: the model batches its state reads in one early shell call. Put the rule at the step that hands off (the end of the preceding check), and verify it with a grep of tool-call order, not of the message.
- An adapter or interface written from the contract alone, before any caller uses it, is the cheapest test of the contract: list each point the writer had to guess, and fix those before the final review.
- An order check in a Verified line must name the read as the model actually performs it (`cat`, `Read`, `ls`), not one command (`git show`); a check that finds no match passes vacuously.

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

- docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md — "Territory adapters run in the conductor's main session; flow:docs stays file-based. Rules out adapters behind flow:docs and a local mirror."
- docs/decisions-log/2026-10-29-main-session-grows-less-not-cleared.md — "The /flow:dev conductor keeps context low by growing less, not by asking for /clear at every slice close; a new session is only suggested at a natural pause."
- docs/decisions-log/2026-10-29-slice-execution-runs-in-a-runner-subagent.md — "A slice's execution runs in one background subagent, flow:slice-runner; the main session keeps stops, STATUS.md and commits. No /clear, no /compact."
- docs/decisions-log/2026-10-27-design-is-agnostic-skill-plus-tool-adapter.md — "Design is a tool-agnostic /flow:design skill plus a replaceable tool adapter chosen via the presets chain; rules out one skill fused with Pen."
- docs/decisions-log/2026-10-26-stack-presets-are-replaceable-skills.md — "Stack knowledge ships as opinionated flow:stack-<name> skills; a project or user maps a stack to a custom skill that replaces the shipped one entirely."

## Project lenses

none (no docs/impact-lenses.md)

## Preset rules

none (stack generic, no preset)

Repo root: /Users/spleenteo/Sites/me/flow. Scratchpad for throwaway repos and adapters (never committed): /private/tmp/claude-501/-Users-spleenteo-Sites-me-flow/a55fc672-afea-4698-96d9-3664e936b88f/scratchpad
