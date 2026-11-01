# Slice packet — V5

## The criterion

- **Problem**: Flow reads and writes its documents only at fixed paths under `root:`, so a team whose decisions, product context or guidelines live in another tool (a wiki, a project board) can't point Flow at them, and the files its guidelines cite reach the agents only by chance.
- **Outcome**: a project declares, per kind of document, where it lives, through which adapter and with which writing peculiarities; Flow reads, writes and searches there and brings each agent the sources it needs. With nothing declared, everything stays under `docs/` as today.
- **Less about**: rewriting Flow's steps (review, gate, close); shipping adapters for specific services inside Flow; deciding for a project which document goes where; copying a territory's content into the repo.
- **Measure**: a document or change serves this work if it lets an agent or a teammate reach the right source without a human relaying it, leaves an unconfigured project behaving exactly as before, and puts no token or public share link in a repo.

## Slice V5 (from slices.md)

## V5 — Writes through territories

**In**
- Decisions on an external territory, from every writer (the conductors and `flow:grill`): `flow:docs` composes the decision as a dated file alone in a scratch folder whose absolute path the request gives, runs `decisions-check` on that folder, and returns the path with no sha; the conductor hands it to the adapter's `write`. A status change first reads the external decision into such a file, then the same.
- The changelog on an external territory: `changelog-write` composes the entry from the excerpt of past entries (V4), the adapter writes it, and the commit holds only the version file. A **Decision** line links the external document by an identifier or a login-only link; it is not dropped because `decisions-log/` is missing.
- Profile writes to an external guidelines territory (the `## Tests` lines) go through the adapter and may come back `proposed`.
- `written` or `proposed <link>`: a proposal is appended under `## Pending proposals` in `STATUS.md` (added to the template in `shared/work.md`); the close's report lists them in one line, its sequence unchanged.
- Docs: `docs/architecture.md`, `skills/docs/SKILL.md`'s request table (`excerpts:`, scratch path, no-sha returns).

- Carried from V4 (rulings): the "cite as `product:<name>`, never copy" rule goes inside the block `framing-doc` and `shaping` receive, not only in the conductor's text, so product text never lands in a committed `frame.md` or `shaping.md`.
- Carried from V4 (spike, before the plan's execute tasks): whether a background `flow:slice-runner` may `Read` a packet in a `mktemp -d` scratch folder outside the working directories under default permissions (a prompt surfacing in the main session, a denial, or silent access), against the alternative `$(git rev-parse --git-dir)/flow-scratch/` (inside the checkout, never committed; check whether writes there prompt). The spike's outcome picks the place and is proposed as a decision.

**Out**: archive (V6).

**Done**
- Test adapter with `write: comment` on `decisions`: recording a decision returns `proposed`, `STATUS.md` lists it, the close's report names it. With `write: direct`, the document appears in the external folder with its key/value table.
- A decision recorded from `flow:grill` lands on the territory too.
- A status change on an external decision updates its fields in the external folder.
- With `changelog` on the test territory, `changelog-write` puts the entry there with its Decision line; `.version` is bumped in the repo and committed alone.
- A decision that fails `decisions-check` is not handed to the adapter.
- No `territories:` → decisions and changelog are written and committed as today.
- Gate green.

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
- A headless check of "a resume reloads X" runs in a fresh session, never with `--resume`: a resumed session carries the earlier reads and copies them instead of reading again.
- A headless prompt that says "write nothing" also forbids the scratch copies an adapter makes, so the model works around the adapter; scope it: "write nothing in the repo; the scratch folder outside it is allowed".
- A shell command a plan dictates for a skill is run in a throwaway repo before it is written in: on Apple git, `\b` in `git grep -E` matches nothing, a plain pathspec `*` crosses `/`, `git cat-file -e` is silent when found, `git grep` prints in tree order, and a loop's last failing test sets the block's exit code.
- An instruction meant for a tool the conductor invokes goes inside the block the tool receives; a rule addressed to the conductor ("never copy it") does not reach the tool's prompt.
- A read the skill only mentions in a parenthesis is skipped headless; state it as an obligation at the step, with its condition.
- On macOS, `mktemp -d` ignores `TMPDIR`; a check that expects scratch files under `$TMPDIR` checks "outside the repo" instead.

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
