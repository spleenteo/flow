# The work

The shared definition of a work's home, state, close and archive, read by `/flow:dev` and `/flow:project`. It is not a skill: the conductors say *when* each of these things happens, this file says *how*. Two layouts, and every rule below names the one it applies to:

- **repo**: a code repository; works live in `<root>/work/`, where `<root>` is `docs` unless `.flow.yml` sets `root`. Used by `/flow:dev`.
- **project**: a folder the user names when opening a project, never a code repo's `docs/`. Used by `/flow:project`.

Configuration (repo) lives in `.flow.yml` at the repository root. An older configuration file is converted by the [upgrade](#upgrade), never read. Keys this file uses: `root`, `merge` (`local` | `pr`, default `local`), `changelog` (a path, or a map whose `path` is used; see `flow:docs` § Configuration), `areas` (see [Decisions](#decisions)), `territories` (where each kind of document lives; `shared/territories.md`).

## Home

### Creating the home

**repo.** A work's home is a dated folder, and a branch in the current checkout:

```bash
base=$(git branch --show-current)
git switch -c work/<slug>
mkdir -p <root>/work/$(date +%Y-%m-%d)-<slug>
```

Record `$base` as `base:` in `STATUS.md`: the close needs it to find the merge target in a repo without a remote. A git worktree only when the user asks for one (two works in parallel, a busy checkout); never by default. The user's dev server, editor and browser see the work as it is.

**project.** The home is the folder the user named; inside a documents vault, propose `<vault>/<Name>/`. Create it with `mkdir -p "<home>"` and touch nothing outside it. A slice too big for one plan becomes a sub-project: `S<n>-<slug>/` inside the home, with its own `STATUS.md` and `parent:` set.

### Finding open works

**repo.** A work's `STATUS.md` lives on its branch, so the checkout alone doesn't show works opened elsewhere. Look in both places:

```bash
rg "^phase:" docs/work/*/STATUS.md 2>/dev/null
for b in $(git for-each-ref --format='%(refname:short)' 'refs/heads/work/*'); do
  git ls-tree -r --name-only "$b" -- docs/work | grep '/STATUS.md$' | grep -v '/archive/' |
    while read -r f; do printf '%s  %s  %s\n' "$b" "$f" "$(git show "$b:$f" | grep -m1 '^phase:')"; done
done
```

A work whose `phase:` isn't `done` and whose branch isn't the current one: say so, and offer `git switch work/<slug>` before reloading anything. With uncommitted changes in the checkout, ask first. When `docs/design/` holds files, ask the user to save and close them in the design tool first: a tool holding a file open can write its copy back over the switched branch.

**project.** `rg "^phase:" <home>/STATUS.md <home>/*/STATUS.md 2>/dev/null`.

## `STATUS.md`

The work's state. Update it at the end of every session, and set `updated:` on every write.

**repo:**

```markdown
---
status: open
phase: <frame | shaping | breadboard | design | slicing | impact | slice | done>
slice: null
step: null
work: <slug>
stacks: [<detected>]
tests: <level, only when raised>
base: <branch the work was cut from>
updated: YYYY-MM-DD
tags: [work, <slug>]
description: "<what the user wants to build, in their words>"
---

# Status — <Readable name>

> Update at the **end of every session**: done, remaining, blockers.

**Entry**: <product | technical>, decided on <date> because <visible-outcome test answer>.

## Slices

- [ ] (defined during slicing)

## Pending proposals

<!-- date — kind:name — link, when an adapter returns proposed -->

## Log

<!-- date — step — done — remaining — wall-clock — cost -->
```

**project:**

```markdown
---
status: open
phase: <frame | shaping | slicing | reality | slice | done>
slice: null
step: null
project: <name>
parent: null
appetite: "<time>, <money>"
design: <tool, only when set: pen>
presets: <[design=<skill>], only when set>
updated: YYYY-MM-DD
tags: [project, <name>]
description: "<what the user wants to achieve>"
---

# Status — <Project name>

> Update at the **end of every session**: done, remaining, blockers.

**Entry**: <frame | shaping | slicing>, decided on <date> because <visible-outcome test answer>.

## Slices

- [ ] (defined during slicing)

## Log

<!-- date — slice — what happened — spent (time, money) — remaining — blockers -->
```

**Both.** `work:` (repo) or `project:` (project) is the bare name; the date lives in the folder name. `phase:` says where the work is; when it's `slice`, `slice:` and `step:` say which slice and where in it (`plan`, `review`, `execute`, `close`; in repo, `close` is reached through the ledger's `## Close draft` and never written). `phase:` moves when a definition phase closes, together with `updated:` and a Log line. Without these three fields an interrupted work shows up the next morning as finished, or as never started. (repo) `tests:` is present only when the level was raised for this work, and overrides the project's level for it. (project) `design:` and `presets:` name the tool `/flow:design` uses and its adapter; they live only in the top home (`parent: null`, sub-projects don't carry them), are present only when set (on the user's word, never guessed), and without `design:` only Claude artifacts are offered. After a verification, the Log line states the outcome: *"09-02 — impact — 2 problems, fixed in V2 and V4 — 14 min — $2.10"*.

## `slices.md` and `lessons.md`

**Both.** `slices.md` is the high-level plan. Per slice: what's in, what's out, how it's demonstrated (the **Done**), known gotchas. Every slice ends in something you can see, run or verify; a slice with no visible outcome is a horizontal layer, not a slice. While planning a slice, deviations from its mandate go under **"Deviations found while planning <id>"**. At its close, the narrative goes under the slice as **"<id> — done on <date>"**: what execution revealed (repo: the rulings taken; project: what happened, what surprised, what it cost). Slices are named `V<n>` (repo) or `S<n>` (project); at most nine per level in a project.

`lessons.md` is created at the first slice close: one rule per line, the digest every later subagent reads instead of `slices.md` in full.

**Closing a slice, the bookkeeping.** Tick the slice in `STATUS.md`, set `slice:` to the next one and `step:` to `plan`, add the Log line. These edits, the narrative into `slices.md`, the rules into `lessons.md` and the commit go in one call, chained with `&&` so it commits only when every write succeeded (project: the commit only when the home is under version control). If the call stops before the commit: `git status --short` and `git diff --stat` on the three files, then finish the missing writes, then commit. A closed slice isn't reopened: if a later slice finds an earlier one was wrong, the fix goes into the current slice, stated in `slices.md`, or becomes a new slice at the end (project: if the appetite allows).

**Commits.** repo: commit at each phase close and slice close, `STATUS.md` updated before the commit and in it, with explicit `git add <paths>`, never `-A` or `.`. project: the same when the home is under version control; in a plain folder the files are the record.

## Decisions

**Both.** A decision is the memory of *why*: it outlives the slices, the plans and the works, which get archived. The conductors propose; `flow:docs` writes, checks and commits (its format and its check live in `skills/docs/`).

**When to propose one:**

- a **real alternative was discarded**: in six months someone will propose it again;
- a **known problem was deferred on purpose**: whoever finds it must know it was known, and why it is still there (`kind: deferral`);
- the behaviour **will surprise** whoever reads the code or the plan later: a guard that looks useless, a callback in an unexpected place, a more expensive supplier, a step skipped;
- an **outside constraint** shaped the choice: a platform limit, a client requirement, a permit, a legal rule.

**The test: does the choice outlive the work?** If not, it is a ruling of the slice and goes in `slices.md`, or a rule for later slices and goes in `lessons.md`. Choices without alternatives, style preferences (repo: the files of `<root>/guidelines/`; project: `constraints.md`) and what the code or the plan already says by itself are no decisions.

**Propose, never write unasked.** At the close of a step, and during execution when one shows up: *"This choice discarded X. Record it?"* One question, with the candidate's gist. Decisions are written when taken, never in bulk at the end: by then the reason has faded.

**On a yes**, invoke the Skill tool with skill `flow:docs` and args a header line plus one note line per item (a repeated label gives several items), self-contained since the fork sees no conversation. Repo layout (`work` is the slug, or `null` outside a work; `area` is required: one of `areas` in `.flow.yml`, or a free kebab-case word when the project declares none):

```
op: decision-write, layout: repo, dir: <absolute repo root>, kind: decision, slug: <what-was-decided>, work: <slug>
decision: <what was decided, concretely>
context: <what was on the table, and what forced a choice>
alternatives: <alternative>: <why it lost>
alternatives: <another alternative>: <why it lost>
consequences: <what it costs, what to watch>
reopen-when: <the event that makes it worth revisiting>
references: <link to slices.md, a spike, a ticket>
touches: <src/menu>
touches: <src/api/filters.ts>
area: <one of the .flow.yml areas>
```

Project layout (`dir` is the folder holding the projects, `project` the home folder's name, `slice` `S<n>` or `null`):

```
op: decision-write, layout: project, dir: <absolute folder holding the projects>, project: <home folder name>, kind: decision, slug: <what-was-decided>, slice: S<n>
decision: <what was decided, concretely: names, numbers, dates>
context: <what was on the table, and what forced a choice>
alternatives: <alternative>: <why it lost>
consequences: <what it costs, what to watch>
reopen-when: <the event that makes it worth revisiting>
references: <the slice's section, a quote, a message>
```

A `deferral` swaps `alternatives:` for `why-deferred:`. `NEEDS: <field>` or `FAILED: <reason>`: ask the user for the field, or report it, and resend or stop. A success returns the path and sha: report them.

**On a territory** (`decisions` declared in `.flow.yml`'s `territories:`, repo layout; the contract is `shared/territories.md` § Writes). Before the request: when this session has no scratch folder, make it now (one `mktemp -d`, shared/territories.md § Reads › Scratch folder); then make a fresh one for this write, `mktemp -d "<session scratch folder>/w.XXXXXX"`, and add `scratch: <that folder>` to the header line. `flow:docs` composes the decision there, runs `decisions-check` on that folder, commits nothing and returns `Composed <absolute path> — check: <summary line>`, with no sha. Then hand it to the adapter: `op: write; kind: decisions; where: <where>[; write: <word>]; name: new; file: <path>`, with `op: none` lines for the other kinds it serves. Every adapter request carries `write` when the territory declares it. On `NEEDS:` or `FAILED:` from `flow:docs`, nothing is handed to the adapter. The adapter returns `written <id>` (report `decisions:<id>`), `proposed <link>` or `FAILED: <id> exists` (stop that write, report the id and the composed path, add nothing to `STATUS.md`, no retry). `unmet`, `unreachable` or any other `FAILED:` records nothing: report the adapter's line and the composed file's absolute path, add nothing to `STATUS.md`, and go on (the contract's § Writes › Returns). On `proposed`, inside a work (except a called `/flow:grill`, which returns `proposed <link> (decisions:<name>)` to its caller, and the caller adds the line), add `- YYYY-MM-DD — decisions:<name> — <link>` under `## Pending proposals` in `STATUS.md` (before `## Log`, created when missing; `<name>` is the composed file's name without `.md`). Run the contract's check (`shared/territories.md` § Writes › The pending line) before the append; when it holds, commit the line alone, message `Note a pending proposal on decisions`; else keep it uncommitted and say so. Outside a work, report the link only.

**Status.** A decision is never edited into its opposite. When a new one replaces it, or a deferred problem gets solved, send `op: decision-status` with `file` (relative to `dir`), `status: superseded` and `superseded_by: <new file>`, or `status: resolved` and `resolved_by: <work slug>`, and `note: <one line>`, plus `project` in the project layout. On a `decisions` territory, first `op: read; kind: decisions; where: <where>; name: <id>`, then send `decision-status` with `scratch:` (a fresh folder as above) and `file: <session scratch folder>/decisions/<id>.md`, and hand the returned `Composed <absolute path> — <status> — check: <summary line>` file to the adapter as `op: write; kind: decisions; where: <where>[; write: <word>]; name: <id>; file: <path>`; `superseded_by` takes the new decision's identifier. `FAILED: check: …` hands nothing on. Returns as in **On a territory**, with `written` reported as `decisions:<id>` and `<name>` the id.

**Checking.** When the user asks to check the log, send `op: decision-check` (`layout`, `dir`, plus `project` in the project layout) and show what it returns. When the user asks to copy the check into the repo, for its own gate or CI, add `install: yes` (repo layout only); committing the copy is the user's call.

**Who reads them.** Repo: the impact lens "Memory and debt" and every plan review, through `op: descriptions` with `keywords` and `files`; `/flow:changelog` links the work's decisions; `/flow:archive` leaves them in place. Project: the reality check and every plan review, through `op: descriptions`; `report.md` links them at exit.

## Agents

Flow's own agents live in the plugin's `agents/` folder, one file per role, dispatched with the Agent tool as `flow:<name>`. Both layouts use them. Each definition holds the role's discipline, its model and its effort; what a role reviews or plans always arrives in the packet.

| Role | Agent | Tier | Used for |
|---|---|---|---|
| Planner | `flow:planner` | top | Writes a slice's plan |
| Plan reviewer | `flow:plan-reviewer` | top | Plan reviews, the fidelity check, the action-plan review, the builder check (`flow:brainstorm`) |
| Impact | `flow:impact` | top | Impact and the reality check, with the lenses in the packet |
| Executor | `flow:executor` | mid | One task of a plan |
| Screen checker | `flow:screen-checker` | mid | A drawn screen's check, from exported images (/flow:design) |
| Slice runner | `flow:slice-runner` | top | A slice's execution and the close's drafts (/flow:dev) |
| Task reviewer | `flow:task-reviewer` | mid | A task's commits; top tier for a `critical` task, the final review and a third fix round |

**Tiers.** The conductors speak of the top tier (planner, plan-reviewer, impact, slice runner, the fidelity check, a `critical` task's review, the final review, a third fix round) and the mid tier (executor, other task reviews, the screen checker). Only the agent definitions name model aliases.

**Nesting.** The slice runner dispatches the executor and the task reviewer, so it relies on nested subagents: they nest three layers by default, and `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH=1` turns it off ([nested subagents](https://code.claude.com/docs/en/sub-agents#let-subagents-spawn-their-own-subagents), [env vars](https://code.claude.com/docs/en/env-vars)). With fork mode on, the default, the runner runs in the background and Claude can't wait for it ([foreground or background](https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background), [fork mode](https://code.claude.com/docs/en/sub-agents#turn-fork-mode-on-or-off)). The permission prompts of background subagents surface in the main session ([background subagents](https://code.claude.com/docs/en/sub-agents#tools-permissions-and-results-of-background-subagents)). A subagent can resume another with `SendMessage` ([resume subagents](https://code.claude.com/docs/en/sub-agents#resume-subagents)), which, when deferred, it loads through `ToolSearch`, and stop one with `TaskStop` ([tools reference](https://code.claude.com/docs/en/tools-reference)). When the runner can't dispatch, it returns `NO_NESTING` and the conductor runs the loop in session.

**Model per dispatch**, first hit wins:

1. The user's explicit request in this session, for a role or for everything: pass it as `model`.
2. The preference for the tier, which the conductor's **Models** paragraph shows: pass it as `model`.
3. Nothing: pass no `model`, so the definition holds.

A role whose definition is mid, dispatched on the top tier with no request and no preference, takes `model` and `effort` from the frontmatter of `plan-reviewer.md` in the agents folder (read it). With a preference for the top tier, it takes that model and the same `effort`.

Two environment variables beat all of this, and Flow can't see past them: `CLAUDE_CODE_SUBAGENT_MODEL_FORCE` (with `CLAUDE_CODE_SUBAGENT_MODEL`) forces the model of every subagent ([model choice](https://code.claude.com/docs/en/sub-agents.md#choose-a-model), [one model for all](https://code.claude.com/docs/en/sub-agents.md#run-every-subagent-on-one-model)), and `CLAUDE_CODE_EFFORT_LEVEL` overrides every effort ([effort level](https://code.claude.com/docs/en/model-config.md#set-the-effort-level)). Name them to the user in one sentence when a model they asked for doesn't seem to apply.

**Dispatch contract.** The prompt is the slice packet (or the conductor's own packet) verbatim at the top, then the step's sources, when `.flow.yml` declares some (`skills/dev/SKILL.md` § Sources), then the role's instruction. Paths in a packet or an instruction are expanded absolute paths, never variables: a subagent substitutes nothing. Every dispatch carries the packet in its prompt, except the slice runner, which gets the packet as a file.

**Ledger.** `<work dir>/V<n>-ledger.md`, written by the slice runner (the conductor in the fallback) and committed with the close.

- The head, in one write: first line `# Ledger — V<n>`, second line the result of `printenv CLAUDE_CODE_SUBAGENT_MODEL_FORCE CLAUDE_CODE_SUBAGENT_MODEL`, run once at the start of execute, a third line `Packet: <path>` when the packet is outside the work folder (the path is the `Packet:` field of the instruction).
- Two writes per task. At the first executor return that carries commits: its line `- <task> · <agent> · <model> · <outcome> · <commits>` and its report, with any earlier return of the task held until then. When the task passes: every later line of the task in order (each review, each fix dispatch with its report), then `- T<n>: complete`. A task reopened after `complete` gets one more write when it passes again. Before a `STOP` returns, the lines held so far go in one write. The model is the passed value, or `definition: <alias>` when none was passed, plus `forced` when the variable is set.
- `Ruling: <what> — <why> — <cost if wrong>`, written at once as its own write: a ruling on findings, a split (`T4a`/`T4b`), the user's answer to a `STOP`, written when the runner is dispatched again, and a rebuilt packet's new path, written by the conductor at a resume; with no ledger yet, the conductor writes the head (with its `Packet:` line) and that ruling in one write, head first.
- Each agent's report is appended under its line, trimmed to status, commits, tests run, `Servers:`, concerns.
- On resume at `step: execute` the ledger is reloaded, and tasks marked complete are never dispatched again. A task with commits and no `complete` is reviewed again: its commits in the ledger, plus the later commits whose subject is `Fix review findings on T<n>:`. A task resumed between its two writes is reviewed from round 1, and review verdicts and low findings not yet written are lost. Ledgers written one line per dispatch have the same lines and resume the same way. With every task complete and no tail yet, the final review takes the ledger's shas plus the commits of `git log --format='%h %s' <first ledger sha>..HEAD`, so a final-review fix committed before the tail write is not lost.
- At the end, in one write and in this order: the final review's lines and its fix round, the gate's fix round; any `pending: <Done item>` lines, one each; `Final review: <done | skipped>`; `Gate: <result>`; then `## Close draft` holding `Narrative: <text>`, `Lessons:` (one rule per line) and `Decision candidate: <gist | none>`.

## Closing a work

**repo.** When the last slice is closed and the user confirms the work is complete. Each step asks before running; the user may stop at any point.

1. **Close.** `phase: done`, `status: closed`, final Log line, commit.
2. **Changelog.** Entry and the version file, committed on the branch. The rules (level, entry format, version, commit) belong to `flow:docs`, a forked skill that never asks anything: this step is where the questions are asked and the requests sent. The close of `/flow:dev` and `/flow:changelog` both follow it. In order:
   1. Show `git status --short`, so the user sees what the commit will and won't hold.
   2. The version file missing (`.version` at the repo root, or `version_file` in `.flow.yml`): ask the current version, suggesting `0.1.0` if nothing has been released and `1.0.0` if the product is in production. The answer goes into the requests as `current`.
   3. The branch holds works unrelated to this one: ask whether they ship as one release or two. Two means one slug per release.
   4. Propose. When a `changelog` or `decisions` territory is declared in `.flow.yml`'s `territories:`, make the session scratch folder now if this session has none (this step also runs alone, by `/flow:changelog`). With a `changelog` territory declared, first make one adapter invocation `op: read; kind: changelog`, which also carries the `decisions` list line below when both are on one adapter, else the list has its own (it stops as shared/territories.md § Resolution does) and add `excerpts: <the returned path>` to both requests below, the conductor's copy of the returned file in the scratch folder (shared/territories.md § Reads); `flow:docs` then takes tone and language from it, and, without a `changelog` territory, `changelog-write` writes `<changelog>` (with one, the entry goes to `<scratch>/CHANGELOG.md` and then to the adapter). With `decisions` declared, send `op: list; kind: decisions; where: <where>`; run the contract's anchored match (`shared/territories.md` § Operations) on the returned block, which gives the ids for `decision-ids` below; an empty match leaves `decision-ids` out. Invoke the Skill tool with skill `flow:docs` and args one line of `key: value` pairs, self-contained, since the fork sees no conversation:

      ```
      op: changelog-propose, layout: repo, dir: <absolute repo root>, work: <slug>, base: <the work's base: in STATUS.md>, release: one[, current: <version>]
      ```

      `release` is `one`, or the slugs of the split, comma-separated; `current` is sent only when the version file is missing. It writes nothing and returns one line per release: `<level> → <version>: <title> — <reason>`.
   5. Show those lines and wait for objections. On a `major? …` line, ask: a product generation is the user's call, never the skill's. Adjust level or title as the user says.
   6. Write. Same skill, `op: changelog-write` (with `layout`, `dir`, `work`, `base`, `release`, and `current` and `excerpts` when sent before; with a `changelog` territory, also `scratch: <a fresh mktemp -d "<session scratch folder>/w.XXXXXX">`; with `decisions` declared, `decision-ids: decisions:<id>, …` from the match above), plus `level: <minor | patch | major>` and `title: "<title>"` per release, in the order of `release` and as the user saw them (with a split: `level: minor, patch`, `title: "First" | "Second"`). `major` only if the user confirmed it.
   7. Handle the answer, of the proposal and of the write alike. `NEEDS: <field>`: ask the user for it and resend the request. `FAILED: <reason>`: report it and stop; nothing was written. With `scratch`, `changelog-write` writes the version file without committing it and returns `not committed` in place of the sha; then make the adapter `write` before reporting: `op: write; kind: changelog; where: <where>[; write: <word>]; file: <the changelog: path>`, with no `name`; nothing is handed to the adapter on `NEEDS:` or `FAILED:`. On `written` or `proposed <link>`, first commit the version file alone: `git -C <dir> add <version_file> && git -C <dir> commit -m "Bump the version to vX.Y.Z" -- <version_file>`, whose sha the report carries. Then `written` is reported as `changelog:document`, and `proposed <link>` adds `- YYYY-MM-DD — changelog:document — <link>` under `## Pending proposals`, the check run before the append and the commit as in § Decisions › **On a territory**, message `Note a pending proposal on changelog`. On `unmet`, `unreachable` or `FAILED:`, nothing is recorded: run `git -C <dir> ls-files --error-unmatch <version_file>`: when it finds the file tracked, restore it with `git -C <dir> checkout -- <version_file>`; otherwise (untracked: the write created it) remove it with `git -C <dir> clean -f -q -- <version_file>`, so a re-run asks `current` again. Then report the adapter's line and the `changelog:` path. Skip step 8. Run alone by `/flow:changelog`, the run ends here; inside a work close, the close stops before step 3 (the archive), and the report names the next move: `/flow:changelog` again on the work's branch once the territory is reachable, then `/flow:archive`, then the merge of `work/<slug>` (§ Closing a work, step 4).
   8. Report each returned line `Recorded v<version> — <title> — <sha>; changelog: <absolute path>; missing decisions: …; manual after merge: …` (the `changelog:` path only with `scratch`; with `scratch`, the sha is the version commit's): the version recorded, the decisions the work took that no file records, the manual steps for after the merge. Say that `/flow:archive` comes next.
3. **Archive.** `/flow:archive`, following [Archive, repo](#archive-repo), or [Archive, repo, on a territory](#archive-repo-on-a-territory) when an `archive` territory is declared, committed on the branch. On `proposed`, the close stops before the merge: once the proposal is accepted, the user runs `/flow:archive` again on the work's branch, on its own (its report then offers the merge) or through `/flow:dev`, whose resume finds the closed work by its `— archive:<dir> — ` line (its § On start); on `archived`, the close goes on at step 4.
4. **Merge.** Find the target:
   1. `git symbolic-ref refs/remotes/origin/HEAD`, with `refs/remotes/origin/` stripped;
   2. else the work's `base:`;
   3. else `init.defaultBranch`, only if `git rev-parse --verify <name>` finds it;
   4. else ask.

   With `merge: local` (the default), propose the merge. When `docs/design/` holds files, ask the user to save and close them in the design tool first: a tool holding a file open can write its copy back over the switched branch. Run the merge after an ok:

   ```bash
   git switch <target>
   git merge --no-ff work/<slug>
   ```

   With `merge: pr`, propose a pull request instead and merge nothing. A request from the user overrides either. Push and tag each need their own ok. The branch is deleted only on request.
5. **Report.** The conductor reports the work as its own exit step says, including what was merged, pushed or tagged, or that nothing was, and, when `territories:` is declared, one line `Pending proposals (as recorded, not re-checked): <kind>:<name> <link>, … | none`; once the work's folder has left the repo, the pending lines come from the archive's report.

Code, changelog and version land on the target in one changeset, and the archived work with them once the archive has run. If something after the close needs the work's documents, move them back with `git mv`, or, archived on a territory, `git checkout <archive commit>^ -- <root>/work/<dir>`: they're plain files in git.

**project.** When the last slice is closed and the user confirms the project is complete:

1. `phase: done`, `status: closed`, final Log line.
2. Write `report.md`: what was done, time and money spent against the appetite, the decisions taken (linked), the lessons, what was left out and why. Written for someone who reads it in a year.
3. Offer to archive, following [Archive, project](#archive-project). Never on your own initiative.

## Archive

Archiving moves a finished work out of the way; nothing is lost: the folder moves to `<root>/work/archive/`, or to the home's sibling `archive/` for a project; on an `archive` territory it goes to its copy there, and the repo's git history keeps the removed folder. Never on your own initiative, never an `open` work: if the frontmatter doesn't say `status: closed` and `phase: done`, say so and ask, since the user may have forgotten the frontmatter or the work isn't finished. A folder without `STATUS.md` isn't archived blindly: ask what it holds.

### Archive, repo

A closed work keeps taking up context in every session that explores `<root>/work/`. Its outcome lives in the changelog and the decisions. `<root>/work/archive/` is walled: the project's `.claude/settings.json` denies `Read` and `Grep` on it. Runs on the work's branch, after the changelog and before the merge, so the archived folder lands with the code. The paths in the examples use the default root `docs`; with `root` in `.flow.yml`, substitute it. It doesn't touch `<root>/decisions-log/` (decisions outlive the work) or `CLAUDE.md`; the only file outside `<root>/` it touches is `.claude/settings.json`, with permission.

**0. First time in a repo: the wall.** `grep -n "<root>/work/archive" .claude/settings.json 2>/dev/null`. If absent, propose adding the `deny`, and wait for a yes:

```json
{
  "permissions": {
    "deny": [
      "Read(./docs/work/archive/**)",
      "Grep(./docs/work/archive/**)"
    ]
  }
}
```

If `settings.json` exists, append the two lines to the `deny` array without touching the rest. If the user declines, archive anyway: the folder stays readable, and the report says so. The wall takes effect from the next session. Also create `<root>/work/archived.md` if missing:

```markdown
---
tags: [work, archive, index]
description: "Index of the works archived in docs/work/archive/, a folder Claude doesn't read. This file lives outside it and is the only trace those works existed."
---

# Archived works

> `docs/work/archive/` holds finished works. Claude doesn't read it: `.claude/settings.json` denies `Read` and `Grep` on that path. This file lives outside and says where each work's outcome is. Updated by `/flow:archive`.

| Work | Closed | What it did | Where the outcome is |
|---|---|---|---|
```

**1. Pick the work.** If the user didn't name it, list the candidates:

```bash
for f in docs/work/*/STATUS.md; do
  printf "%-32s %s  %s\n" "$(basename $(dirname $f))" "$(grep -m1 '^status:' $f)" "$(grep -m1 '^phase:' $f)"
done
```

**2. Check the outcome is recorded elsewhere.** With a `changelog` or `decisions` territory declared, that kind's check below runs on what its adapter returns, as [Archive, repo, on a territory](#archive-repo-on-a-territory) step 1 says; a kind not declared runs as written. `<changelog>` is the changelog's path: the string value of `changelog:` in `.flow.yml`, or the `path` of its map, default `<root>/CHANGELOG.md`. `<label>` is `labels.work` (default `Work`). ``rg -n -F -x -e '- **<label>**: `<slug>`' <changelog>``: with no match, run the looser `rg -n -F -e '<slug>' <changelog>`, whose hit counts as recorded and is named in the report as a loose match; if both are absent, the outcome is recorded nowhere; flag it and propose `/flow:changelog` first. `rg -l '^work: <slug>$' <root>/decisions-log/ 2>/dev/null`: the decisions stay out of the archive. If either is missing, don't block: report it and ask whether to archive anyway.

**3. Census of inbound links.** `grep -rn "work/<dir>/" --include="*.md" docs README.md .claude 2>/dev/null`. Each becomes `work/archive/<dir>/`. If the count is high, say so before proceeding.

**4. Mark and move.** Add `archived: YYYY-MM-DD` to the `STATUS.md` frontmatter first: once moved, the file is out of reach. Then:

```bash
mkdir -p docs/work/archive
git mv docs/work/<dir> docs/work/archive/<dir>
```

Always `git mv`, never `mv`: the rename stays readable in history.

**5. Fix links, both directions.** *Inbound*: rewrite the references from step 3; `grep -rn "work/<dir>/" --include="*.md" docs README.md .claude 2>/dev/null | grep -v "archive/"` must return nothing. *Outbound*: the archived files moved one level down, so their relative links into `<root>/` need one more `../`. Don't replace `../` with `../../` blindly: for each broken link, add a single level and apply only if the file then exists. *Between archived works*: a link from an already archived work to this one had an extra `../`; remove it, with the same try-and-check.

**6. Update the index.** One row in `<root>/work/archived.md`, nothing more:

```markdown
| `<slug>` | <closing date> | <one sentence on what it did> | <changelog entries> · <n> decisions |
```

**7. Commit and report.**

```bash
git status --short
git add docs/work/archive docs/work/archived.md <files with updated links> [.claude/settings.json]
git commit -m "Archive the <slug> work" -- docs/work/<dir> docs/work/archive docs/work/archived.md <files with updated links> [.claude/settings.json]
```

Report: which work, how many links updated, where its trace remains, whether the wall is active, and that its content isn't readable in session from now on.

**Rereading an archived work.** Don't ask Claude to bypass the `deny`. Open the file by hand, or remove the rule from `.claude/settings.json`, restart the session, and put it back afterwards. A work that becomes active again goes back with `git mv`: the archive is a high shelf, not a grave.


### Archive, repo, on a territory

With an `archive` territory declared in `.flow.yml`'s `territories:` (repo layout only), the closed work goes to the territory instead of `<root>/work/archive/`. The adapter's side (the request, the returns, the re-run, the pending line) is [shared/territories.md](territories.md) § Writes › Archive; this section is the git side. The folder is kept as a folder of documents in the territory and removed from the repo in the archive commit. Nothing else changes: no `archived:` marker (the folder is clean, so the territory's copy matches the last commit, and the commit carries the date), no `<root>/work/archived.md` row, no `deny` in `.claude/settings.json`, and step 0 is skipped. The paths use the default root `docs`; with `root` in `.flow.yml`, substitute it.

1. **Pick the work and check the outcome.** As [Archive, repo](#archive-repo) step 1, then its step 2 through the adapters, since `territories:` is declared. Read the contract (`shared/territories.md`) and make the session scratch folder first; this is an obligation, not an option. Send one invocation per adapter before anything is touched: `op: read; kind: changelog` when `changelog` is declared, `op: list; kind: decisions` when `decisions` is declared, and `op: none` for every other declared kind, `archive` included, so `Requires` is checked first. The changelog check runs on the conductor's copy of the returned file. The decisions check is the contract's anchored match (§ Operations) on the returned block. Stop as [shared/territories.md](territories.md) § Resolution says, with nothing archived. A kind that is not declared keeps its repo check.
2. **Clean check.** `git status --porcelain --ignored -- <root>/work/<dir>` must print nothing: `git rm -r` leaves untracked and ignored files behind, and the territory would get files the commit does not hold. If it prints anything, list it and ask the user to commit, move or delete those files, then run the check again; never go past this step while it prints anything.
3. **Keep the pending lines.** Note the lines under the work's `## Pending proposals` in `STATUS.md` other than `archive:<dir>`: the folder leaves the repo, and the report carries them. On any re-run where `STATUS.md` holds a `— archive:<dir> — ` line, clear it first, so the folder matches the container the accepted proposal produced: run `git ls-files --error-unmatch <root>/work/<dir>/STATUS.md && git diff --quiet HEAD -- <root>/work/<dir>/STATUS.md`, the guard of the contract's pending line (it holds after step 2); then remove that line and, when it was the section's only entry, the `## Pending proposals` heading and the blank lines the append added, so `STATUS.md` is exactly what was sent; then run `git add <root>/work/<dir>/STATUS.md && git commit -m "Clear the pending proposal on archive" -- <root>/work/<dir>/STATUS.md && git rev-parse HEAD` and keep the sha it prints as `<clear commit>` for step 6. If the guard exits non-zero, stop and report, with nothing sent. With no such line, go on to step 4.
4. **Census of inbound links.** `grep -rn "work/<dir>/" --include="*.md" docs README.md .claude 2>/dev/null | grep -v "^docs/work/<dir>/"`.
5. **Sibling census.** `grep -rn "\.\./<dir>/" --include="*.md" <root>/work | grep -v "^<root>/work/<dir>/"`: other works link it as `../<dir>/`.
6. **Archive.** First run `test -f <root>/work/<dir>/STATUS.md && grep -qx 'status: closed' <root>/work/<dir>/STATUS.md && grep -qx 'phase: done' <root>/work/<dir>/STATUS.md`; if it exits non-zero, stop there with nothing sent. Then send `op: archive; kind: archive; where: <where>[; write: <word>]; folder: <absolute path of <root>/work/<dir>>`, with `op: none` lines for the adapter's other declared kinds. `archived <name>` goes on. `proposed <link>` ends the procedure here: nothing is removed or rewritten, `- YYYY-MM-DD — archive:<dir> — <link>` goes under the work's `## Pending proposals` by the contract's § Writes › The pending line (message `Note a pending proposal on archive`). The report says to run `/flow:archive` again once the proposal is accepted. `unmet`, `unreachable` or `FAILED: <reason>` is reported and the archive stops, with nothing removed. When step 3 cleared a line: on `unmet`, `unreachable` or `FAILED:`, restore it first with `git checkout <clear commit>^ -- <root>/work/<dir>/STATUS.md && git commit -m "Restore the pending proposal on archive" -- <root>/work/<dir>/STATUS.md`; on the guard's stop, run that line only if `git diff --quiet <clear commit> HEAD -- <root>/work/<dir>/STATUS.md && git diff --quiet <clear commit> -- <root>/work/<dir>/STATUS.md` exits 0, else touch nothing. Either way the report names the cleared line, since the proposal may still be open.
7. **Remove the folder.** Guarded, so an empty `<dir>` or an open work removes nothing:

   ```bash
   test -n "<dir>" && test -f <root>/work/<dir>/STATUS.md && grep -qx 'status: closed' <root>/work/<dir>/STATUS.md && grep -qx 'phase: done' <root>/work/<dir>/STATUS.md && git rm -r -q -- <root>/work/<dir>
   ```

   If the line exits non-zero, stop and report it: the territory holds `archive:<name>`, nothing was removed or rewritten.
8. **Rewrite the inbound references** from both censuses as plain text, never a link and never the adapter's link. `<where>` is the territory's `where` as written in `.flow.yml`. A Markdown link with text `<text>` and a target under `work/<dir>/` or `../<dir>/` becomes `<text> (archived on <where>: <name>)`; any other mention of the path becomes `archived on <where>: <name>`. Both censuses, run again without their `grep -v`, must print nothing.
9. **Commit.** `git add <files with updated links>`, then `git commit -m "Archive the <slug> work" -- <root>/work/<dir> <files with updated links>`. The removal is already staged; the pathspec keeps anything else staged out of the commit.

A re-run follows the contract's § Writes › Archive.

Report: the work, `archive:<name>`, the number of links rewritten, and the pending lines kept, which [Closing a work](#closing-a-work) step 5 reports in place of `STATUS.md`, and that links held on other territories were not rewritten. The way back: `git checkout <archive commit>^ -- <root>/work/<dir>`. The report ends with the next move, the merge of `work/<slug>`. Invoked from `/flow:dev`'s close, the close goes on at Step 6 move 4. Otherwise, offer to run [Closing a work](#closing-a-work) step 4 (`/flow:dev` Step 6 move 4) now: the merge as `merge:` says, after its own ok, with push and tag each asking separately; if the user declines, give step 4's commands instead.

### Archive, project

1. Write `archived: YYYY-MM-DD` in the home's `STATUS.md` first.
2. Move the home to the sibling `<home>/../archive/<name>/`: `git mv` if the folder is in a git repo, else `mv`. `report.md` stays inside.
3. No wall. No `archived.md`, unless the parent folder is a git repo: then add the same one-row entry to `<home>/../archived.md`, creating it with the header above (paths adjusted).
4. Report where it went.

## Release check

Tells the user when a newer Flow is out. Runs once per invocation of `/flow:dev` and `/flow:project`, right after the dependency check; it never blocks, asks or installs. The installed version is the plugin's `.version` (the skill gives its path); the latest release is the highest `v*` tag of Flow's public repo:

```bash
git -c http.lowSpeedLimit=1000 -c http.lowSpeedTime=5 ls-remote --tags --refs https://github.com/spleenteo/flow 'v*' 2>/dev/null |
  sed 's#.*refs/tags/v##' | sort -t. -k1,1n -k2,2n -k3,3n | tail -1
```

When the latest is higher than the installed version, compared number by number, the first message to the user carries one line: "Flow <latest> is out (installed: <version>). To update: `claude plugin marketplace update flow && claude plugin update flow@flow`, then restart Claude Code." Silent when the versions match, when the installed one is higher (a development copy), and when the command prints nothing (offline, or the repo unreachable).

## Upgrade

Old traces are what an earlier version of this plugin left behind under other names. The upgrade rewrites the ones Flow owns, and the files `sources:` names, and points at the ones it doesn't. It is a run of its own: when `/flow:dev` or `/flow:project` finds old traces, it says so, offers the upgrade, and nothing else happens in that run (no profile, no first-run questions, no work opened or resumed). The next run starts on the upgraded state. `/flow:changelog`, `/flow:archive` and `flow:docs` never upgrade: facing an older configuration file, they stop and point to `/flow:dev`.

**repo.** Looked for on the default branch (the target of Closing a work, step 4), not in the checkout, by one `bash` block that prints names or lines only (`<default>` is that branch). `.flow.yml` is listed first so its `root:` wins; the default root is `docs`:

```bash
r=$({ git grep -h '^root:' <default> -- .flow.yml; git grep -h '^root:' <default> -- .devflow.yml; } | head -n 1); r=${r#root:}; r=${r# }; r=${r:-docs}
git cat-file -e <default>:.devflow.yml 2>/dev/null && echo .devflow.yml
git grep -l '^stack:' <default> -- .flow.yml .devflow.yml
git grep -l -F '/devflow-archive' <default> -- "$r/work/archived.md"
git grep -l -F 'Devflow passes this file' <default> -- ":(glob)$r/*.md"
git grep -n -E '/devflow|/projectflow|\.devflow\.yml' <default> -- CLAUDE.md .claude/CLAUDE.md CLAUDE.local.md
git grep -n -w -E 'devflow|projectflow' <default> -- .claude/settings.json
git ls-tree --name-only <default> .claude/skills/
git grep -n '^sources:' <default> -- .flow.yml
for b in $(git for-each-ref --format='%(refname:short)' refs/heads/work/); do { git cat-file -e "$b:.devflow.yml" 2>/dev/null || git grep -q '^sources:' "$b" -- .flow.yml; } && echo "$b"; done
echo "upgrade check: done"
```

No file is printed in this check: the output carries names and matching lines, never a file's content. Of `sources:`, only the key line is a trace; the reads and lines below happen only in an upgrade it triggered.

**`sources:`** (repo only: `/flow:project` has none)

A `sources:` key in `.flow.yml` is converted: a `{territory, name}` entry is dropped and named, whatever its `when` holds; a `path` entry with at least one glob becomes a file of `<root>/guidelines/`, moved there with `git mv` and given a frontmatter of `applies` (its globs, merged across entries sharing a path) and a `description` drafted from its first heading, for the user to rewrite; when the destination `<root>/guidelines/<basename>` is already taken, the file gets its frontmatter where it is and is named, not moved; a `path` entry with only `{step}` or `{phase}` values and a missing path are dropped and named, and their files stay where they are; the key is removed. References to a moved file, its relative links that break and the guidelines file's sections beyond its four are pointed at, not edited.

| Found | Rewritten |
|---|---|
| `.devflow.yml` | Renamed to `.flow.yml`. When both exist, `.flow.yml` wins on conflicting keys, the keys only the old file has move into it, and the old file is removed |
| `stack: <name>` in the configuration | `stacks: [<name>]`; dropped when `stacks:` is already there |
| `<root>/work/archived.md` whose header cites `/devflow-archive` | That line says `/flow:archive` |
| A guidelines file under the root whose opening says "Devflow passes this file" | That sentence says `/flow:dev` |
| `sources:`, a `{territory, name}` entry | Dropped and named, whatever its `when` holds: the kind lists its own documents |
| `sources:`, a `path` entry with at least one glob | `applies` with the globs and a drafted `description`, marked for the user to rewrite. The block is merged into an existing frontmatter block (an existing `description` or `applies` is kept, and the globs are named in the message); the entry's globs are merged across entries sharing a path. The draft is `description: "<first heading, without its #s; \" becomes '>"  # drafted by the upgrade: rewrite it`, the first heading being looked for after the frontmatter block; with no heading, the basename without `.md`. The file is moved with `git mv` to `<root>/guidelines/<basename>` unless it is already under that folder. When that destination exists on the default branch, or two entries' different paths share the basename, the frontmatter is written, the file stays, and the message names it with the `git mv` for the user |
| `sources:`, a `path` entry with no glob left (only `{step}` or `{phase}` values) | Dropped and named; the file stays |
| `sources:`, a path missing or untracked on the default branch | Dropped and named |
| `sources:` | The key removed, with its block-style lines |

Pointed at and never edited, each as `<file>:<line> — <new name>`, or as the command that removes it:

- lines of `CLAUDE.md`, `.claude/CLAUDE.md` and `CLAUDE.local.md` naming `/devflow`, `/devflow-docs`, `/devflow-archive`, `/projectflow` or `.devflow.yml`;
- `Skill(...)` entries for those skills in `.claude/settings.json`;
- project-scope copies of those skills under `.claude/skills/`;
- inbound references to a moved file, as `<file>:<line> — <what>`: `@` imports in `CLAUDE.md` files and Markdown links;
- a moved file's relative links that break from its new folder, as `<file>:<line> — <what>`;
- the sections of the guidelines file beyond References, Tests, Gate and Deviations, as `<file>:<line> — <what>`, candidates for the folder;
- open `work/*` branches whose tree still holds an old file or a `sources:` key: nothing refreshes them on its own. The command, for the user to run after the upgrade merges: `git switch work/<slug> && git merge <default>`.

**project.** In the home and its sub-projects, rows of `slices.md` and `S<n>-plan.md` reading "devflow work `<slug>` in `<repo>` merged" become "flow:dev work `<slug>` in `<repo>` merged".

**Asking.** One message: each change as `file — what changes`, then the pointed-at lines, then one question. With uncommitted changes in the checkout, say so and ask to commit or stash first (never stash it yourself). This check comes before any branch switch, the pending-merge proposal included. Nothing is written before the ok.

When the check printed the `sources:` line, the message also lists each move, drop and the key removal as `file — what changes`. The reads for it happen after the check, line-only, in one call that first recomputes `r` with the check block's first line (shell state doesn't carry between calls); they are `git show <default>:.flow.yml` (block style puts the entries below the key line); per path, `git cat-file -e <default>:<path>`, the file's frontmatter block only (`git show <default>:<path>` through `awk 'NR==1&&$0!="---"{exit} NR>1&&$0=="---"{exit} NR>10{exit} NR>1'`), and the draft's heading, `git grep -m1 -n '^#' <default> -- <path>` run past that block (a line, never the file); inbound references, `git grep -n -F '<basename>' <default> -- '*.md' ':!<path>' ":!$r/work/**"`, keeping lines where the name sits in an `@` import or a `](` link; outbound links, `git grep -n -o -E '\]\([^)]+\)' <default> -- <path>`, each relative one resolved from the new folder and named when `git cat-file -e <default>:<resolved>` fails; extra sections, `git grep -n '^## ' <default> -- "$r/development-guidelines.md"`, beyond the four. When `.flow.yml` also declares `guidelines` under `territories:` (seen in the `git show`), the message says the moved files won't be read while that territory stands.

**Writing, repo.** On the ok, the reminder comes first. When `docs/design/` holds files, ask the user to save and close them in the design tool first: a tool holding a file open can write its copy back over the switched branch. Then cut `flow-upgrade` from the default branch (outside `work/`, so no scan takes it for a work), write the changes (a `git mv` of `sources:` runs here, on `flow-upgrade`, after the ok, and the file is read there whole to merge the frontmatter at its new path), commit with explicit paths as `Upgrade to Flow's current conventions` (one commit; the pathspec holds both the source and the destination of every move, so the staged deletion doesn't follow the switch back), and switch back to the starting branch. Then propose merging it into the default branch locally, with its own ok, by the commands of Closing a work, step 4; the target is the default branch the detection found, never a work's `base:`. Push stays the user's. An existing `flow-upgrade` branch means the upgrade is pending: say so, propose the merge, never cut it again. **Writing, project.** Rewritten in place; committed only when the home is under version control, with explicit paths.

**Declined.** The run stops there: nothing is opened or resumed, and nothing is written. Say that the old configuration (repo) or the old rows (project) are no longer read and that the upgrade will be offered again next time.

**Resume on an old file.** **repo**, on a resume in `/flow:dev` (project homes have no branches), before any write. Read the work's branch, not the current checkout, since the user may still be on the default branch: `git show work/<slug>:.devflow.yml`, and likewise the other old files of the first table, and `git grep -n '^sources:' work/<slug> -- .flow.yml`. When the branch still holds one, nothing runs on it:

- **(a)** The default branch has no traces left: propose merging the default branch into the work's branch (`git merge <default>`), with its own ok, and stop until it is done. The clean-tree check comes before the merge's switch. When `docs/design/` holds files, ask the user to save and close them in the design tool first: a tool holding a file open can write its copy back over the switched branch.
- **(b)** The old file exists only on this branch, because the default branch never had it: stop, say so, and point to the same rewrite as the first table, for the user to make on that branch: the rename (`git mv .devflow.yml .flow.yml`) when `.flow.yml` is absent, else the key merge into the existing `.flow.yml` and removal of the old file; and `stack:` to `stacks:`, and the `sources:` rows of the table.
