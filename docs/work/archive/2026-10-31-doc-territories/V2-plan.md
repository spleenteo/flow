---
work: doc-territories
slice: V2
updated: 2026-10-31
tags: [work, doc-territories, plan]
description: "Plan of V2: sources: in .flow.yml; path-scoped sources ride in the slice packet, step- and phase-scoped ones in the instruction after it"
---

# V2 — Guidelines as a set of sources — Plan

**Mandate**: [V2-packet.md](V2-packet.md) · **Guidelines**: [development-guidelines.md](../../../development-guidelines.md) · **Lessons**: [lessons.md](lessons.md) · **Execution rules**: [executor.md](../../../../agents/executor.md)

**Summary**
1. T1 defines `sources:` once in `skills/dev/SKILL.md` § Conventions (syntax, matching, placement, bad entries) and hooks it into every place a packet or an instruction is built: Step 3, impact, the slice packet, plan, review and its re-match, the first stop, execute move 4, the loop, close move 1, the resume table.
2. T2 gives `flow:slice-runner` the two source fields and adds the source block to the dispatch contract in `shared/work.md`.
3. T3 points the guidelines template's `## References` at `sources:` and documents the key in the README and `docs/architecture.md`. T4 makes the conductor build packets and instructions with `claude -p` in throwaway repos in the scratchpad and checks the Done items on its output. Nothing from it is committed.

**Files**: skills/dev/SKILL.md, agents/slice-runner.md, shared/work.md, skills/dev/development-guidelines.template.md, README.md, docs/architecture.md

## The slice, decided here

- Syntax: `sources: [{path: docs/guides/testing.md, when: ["spec/**"]}, {path: docs/notes/pricing.md, when: [step: plan]}]`. `path` is repo-relative. `when` is one value — a quoted glob, or `{step: <name>}` / `{phase: <name>}` (`{step: plan}`, `{phase: shaping}`), with the names from the mandate — or else the list form, a flow list of such values, mixing allowed: `when: ["spec/**", step: execute]` (inside the list, `step: plan` is a single-pair mapping). A bare `when: step: plan` inside a flow map is invalid YAML.
- Glob: `*` matches within one path segment, `**` matches any number of segments. A file path matches when the glob matches it. A directory path (trailing `/`) matches when the glob's literal prefix (its segments before the first wildcard), compared by whole segments, equals the directory or lies above or below it. A glob that starts with a wildcard has an empty prefix and matches files only. Reason: otherwise `"**/*_spec.rb"` would load into the packet of every slice that names a folder (the Gotcha). So `spec/` matches `"spec/**"` and `app/` doesn't.
- The paths matched are the ones the decisions' `files:` already use: the repo paths the slice's section names. After the plan, the paths of the planner's `Files:` are matched again. A new match is appended to the packet's `## Sources` before the review dispatch. From then on, the packet holds it for the review, `V<n>-packet.md` and execution.
- Packet: a `## Sources` section after the guideline sections, one `### <path>` per source with the file's text verbatim. Instruction: a `## Sources for step: <name>` block (or `phase: <name>`) in the same form, placed after the packet and before the role's instruction. A source already in the packet isn't repeated in an instruction. With no `sources:`, nothing is added, not even an empty heading.
- Where step sources go: `plan` → the planner; `review` → the plan reviewer; `execute` → every loop dispatch (executor, task reviewer, final review); `close` → the runner itself, for close moves 1–2, and the fix round of the gate, which gets the `step: close` block only (not the `execute` one). Phase sources go into the phase tool's prompt, below the criterion (§ The criterion travels), and at impact after each agent's packet. When the conductor writes `frame.md` itself, it reads them.
- The runner gets paths, not text: `Execute sources: <absolute paths, comma-separated | none>` and `Close sources: <… | none>`. It reads them and builds the block itself, as it already does with the packet file. With no `sources:` key, both fields are left out of the runner dispatch entirely.
- An entry whose `path` is missing, isn't inside the repo, or whose `when` value is unknown is skipped and named in the next message to the user. It never stops a step. The first stop names the sources the packet carries, with their line counts, in one line: that is how the Gotcha's packet size stays visible.
- `/flow:project` is untouched: its homes have no `.flow.yml`.

## Tasks

### T1 — The conductor reads `sources:` and places them · critical

**What**: in `skills/dev/SKILL.md`:
- ~:49: add `sources` to the key list ("a flow list of `{path, when}`; see § Sources below").
- After ~:49: a new paragraph **Sources.** stating the decisions above: syntax with the example, glob and directory rule, the two placements and their headings, the step and phase recipients, no repetition, bad entries, nothing added without the key.
- ~:255 (The criterion travels): add the phase sources below the criterion. ~:265 (impact): add the impact sources after each agent's packet.
- ~:279 (slice packet): add "the path-scoped sources that match the paths the slice's section names (§ Sources)".
- ~:283-285 (`step: plan`): the instruction is preceded by the `step: plan` block. ~:291 (`step: review`): re-match the sources against the planner's `Files:`, as the decisions are re-matched, append what is new, and send the `step: review` block. ~:301 (first stop): add the line naming the sources and their line counts.
- ~:312 (execute move 4): add the fields `Execute sources:` and `Close sources:` after `Final review:`, present only when `.flow.yml` has `sources:`. ~:327, :329, :332 (the loop): the executor, task-reviewer and final-review dispatches carry the `step: execute` block after the packet. ~:338 (close move 1): the fix-round executor and its review carry the `step: close` block only.
- ~:182 (resume table, plan row): add "and the sources matched by the slice's paths". ~:183 (review row): add "its **Files** line re-matches the decisions and the sources".

**Files**: skills/dev/SKILL.md
**Verified**:
- `rg -n 'Execute sources:|Close sources:' skills/dev/SKILL.md` → at least one match: the move 4 line, holding both fields.
- `rg -n 'Sources for step:' skills/dev/SKILL.md` → the Sources paragraph, plus at least one line each in § step: plan, § step: review and #### The loop.
- `rg -n '"spec/\*\*"|\{step: plan\}' skills/dev/SKILL.md` → at least one match, in the Sources paragraph.
- `rg -n '`sources`' skills/dev/SKILL.md` → at least one match, on the key list line.
- Read `git diff 57b9c30 -- skills/dev/SKILL.md`: every `+` sentence is conditional on `sources:` or names its block. The only `-` lines are the sentences being extended.
- `claude plugin validate .` → passes with the expected warnings only.

**Done**: every packet and instruction the conductor builds says where sources go, and nothing changes without the key. **Interfaces**: produces the headings `## Sources` and `## Sources for step: <name>` / `phase: <name>`, the fields `Execute sources:` and `Close sources:`, and the § Sources paragraph. T2, T3 and T4 use them word for word.

### T2 — The runner and the dispatch contract carry the step's sources · depends on: T1

**What**: `agents/slice-runner.md`: under "What comes in the instruction", add `Execute sources:` and `Close sources:` (absolute paths, comma-separated, or `none`) after `Final review:`, both present only when the project declares `sources:` and absent otherwise. Step 2 says: with `Execute sources:` other than `none`, read those files and put a `## Sources for step: execute` block (one `### <path>` per file, its text verbatim) after the packet in every executor and task-reviewer dispatch; `Close sources:` likewise for close moves 1–2 (read before the close draft) and the gate's fix round (that block only, not the `execute` one), under `## Sources for step: close`. `shared/work.md` § Agents › Dispatch contract (~:202): "then the role's instruction" becomes "then the step's sources, when `.flow.yml` declares some (`skills/dev/SKILL.md` § Sources), then the role's instruction".
**Files**: agents/slice-runner.md, shared/work.md
**Verified**: `rg -n 'Execute sources:|Close sources:' agents/slice-runner.md` → the instruction list and step 2. `rg -n 'step.s sources' shared/work.md` → the Dispatch contract line. Read-through: the runner's field names and block headings are identical to T1's lines in § step: execute.
**Done**: a runner dispatched with the fields passes the `execute` sources to every task dispatch and to no other.

### T3 — Template, README and architecture document `sources:` · depends on: T1

**What**: `skills/dev/development-guidelines.template.md` `## References` comment: keep it prose for humans and add "A document Flow's agents must read goes in `sources:` of `.flow.yml`, with when it applies." `README.md` ~:175: add `sources: [{path: docs/guides/testing.md, when: ["spec/**"]}, {path: docs/notes/pricing.md, when: [step: plan]}]` to the YAML block, with a short comment. After the changelog paragraph (~:178), add one paragraph covering `path`, `when` (one value, a quoted glob or `{step: <name>}` / `{phase: <name>}`, otherwise the list form), the packet versus the instruction, and the size cost of a path-scoped source. `docs/architecture.md`: add `sources` to the ~:196 `.flow.yml` row. In the ~:133 Slice loop bullet, add "the matching sources" to the packet list and one clause on the step sources after it.
**Files**: skills/dev/development-guidelines.template.md, README.md, docs/architecture.md
**Verified**: `rg -n 'sources' skills/dev/development-guidelines.template.md README.md docs/architecture.md` → at least one match in each file, and the README's YAML line parses as the T1 example word for word.
**Done**: a reader of any of the three learns where a must-read document is declared and what it costs.

### T4 — Build packets and instructions in throwaway repos · depends on: T1, T2

**What**: verification by a `claude -p` run that applies the conductor's rules and prints what it would send, dispatching nothing. That is the closest headless stand-in for `/flow:dev`, whose packet-building happens in the main session. `$S` = `/private/tmp/claude-501/-Users-spleenteo-Sites-me-flow/a55fc672-afea-4698-96d9-3664e936b88f/scratchpad`. `$S/v2-repo` (`git init -b main`) holds: `.flow.yml` with `stacks: [generic]` and T1's `sources:` example; `docs/guides/testing.md` with the line `TESTING-GUIDE-7Q: one behaviour per spec` and `docs/notes/pricing.md` with `PRICING-NOTE-4K: prices in cents`; `docs/development-guidelines.md` (`## Tests`, `Level: none`, `Sensitive areas:`; `## Gate` with `true`); `docs/work/2026-10-31-shop/` with `STATUS.md` (`work: shop`, `phase: slice`, `slice: V1`, `step: plan`), `lessons.md` holding one rule, and `slices.md`, where `## V1 — Cart total` names `spec/` and `app/models/cart.rb` and `## V2 — Shop page` names only `app/` and `app/controllers/shop_controller.rb` (so the directory non-match is tested); `app/models/cart.rb`, `app/controllers/shop_controller.rb`, `spec/.keep`. One commit.

Copy it to `$S/v2-none`, drop the `sources:` line and commit. After T2 (and T3, if it ran before T4) is committed, extract the V2 Flow: `mkdir -p $S/flow-v2 && git -C /Users/spleenteo/Sites/me/flow archive HEAD | tar -x -C $S/flow-v2`. Then extract the baseline from the commit V2 started on: `mkdir -p $S/flow-base && git -C /Users/spleenteo/Sites/me/flow archive 57b9c30 | tar -x -C $S/flow-base`. Each run pipes the prompt on stdin (lesson 1) and never passes a permission mode: `printf '%s' "$P" | (cd "$R" && claude -p --plugin-dir "$F" --add-dir "$F" --allowedTools "Read Bash(rg:*) Bash(ls:*)") > $S/out-<n>.txt`. `$P` reads: "You are checking a skill's rules, not running it. Read `$F/skills/dev/SKILL.md` (§ Conventions, § Step 4 with its steps and #### The loop), `$F/agents/slice-runner.md` and `$F/shared/work.md` § Agents. As the conductor in this repo, work `shop`, slice `<V>`, print exactly these blocks and nothing else, each opened by its own line: `=== PACKET (as built before the plan)`, `=== PLANNER`, `=== REVIEW-ADDITIONS`, `=== PLAN-REVIEWER`, `=== SLICE-RUNNER`, `=== EXECUTOR`, `=== TASK-REVIEWER`. A block after PACKET holds only what follows the packet in that prompt; REVIEW-ADDITIONS holds what is appended to the packet before the review, or `none`. The planner returned `Files: <list>`. The executor's task is T1 `Add the cart total`, Files `<first path>`. Invoke no skill, dispatch no agent, write no file. Decisions: none (no decisions-log/). No preset." Runs:
1. `v2-repo`, `V1`, `Files: app/models/cart.rb, spec/models/cart_spec.rb`, `F=$S/flow-v2`.
2. `v2-repo`, `V2`, `Files: app/controllers/shop_controller.rb`, `F=$S/flow-v2`.
3. `v2-repo`, `V2`, `Files: app/controllers/shop_controller.rb, spec/requests/shop_spec.rb`, `F=$S/flow-v2`.
4. `v2-none`, as run 1, `F=$S/flow-v2`.
5. `v2-none`, as run 1, `F=$S/flow-base`.

A permission denial or a failed launch → stop and return `BLOCKED` with the output. Never change the permission mode; the conductor then runs T4 in the main session after the user's ok.
**Files**: none (scratchpad only; no commit)
**Verified** (`blk() { awk -v b="$2" '/^=== /{c=$2; next} c==b' "$1"; }`):
- Run 1: `blk $S/out-1.txt PACKET | rg -c TESTING-GUIDE-7Q` → 1 or more. `blk $S/out-1.txt PLANNER | rg -c PRICING-NOTE-4K` → 1 or more. `awk '/^=== /{c=$2; next} c!="PLANNER"' $S/out-1.txt | rg -i pricing` → no output. `blk $S/out-1.txt SLICE-RUNNER | rg 'Execute sources: none'` → one line.
- Run 2: `rg TESTING-GUIDE-7Q $S/out-2.txt` → no output. `blk $S/out-2.txt PLANNER | rg -c PRICING-NOTE-4K` → 1 or more.
- Run 3: `blk $S/out-3.txt PACKET | rg TESTING-GUIDE-7Q` (the PACKET (as built before the plan) block) → no output. `blk $S/out-3.txt REVIEW-ADDITIONS | rg -c TESTING-GUIDE-7Q` → 1 or more.
- Run 4: `rg -i 'sources|PRICING-NOTE|TESTING-GUIDE' $S/out-4.txt` → no output.
- Runs 4 and 5, read side by side: the same blocks, the same packet sections in the same order, the same runner fields, with no source field in either; the differences are wording only. This is a read-through, since the model's wording varies between runs.

**Done**: V2's first two Done items hold, short of the Gate. Outputs and paths go in the report.

## Order

T1 first. Then T2 and T3 side by side (separate files). T4 after T2. The Gate follows all four.

## Deviations from the mandate

- The first stop gains one line naming the packet's sources and their line counts. It addresses the mandate's packet-size Gotcha and changes nothing else at that stop.
- The `execute` and `close` sources reach the runner as two new fields, and `shared/work.md`'s dispatch contract changes too. The mandate names neither, but without them the runner, which builds the task dispatches, never sees those sources.
- A directory path matches a glob only by its literal prefix, so a glob starting with a wildcard never matches a directory. The mandate leaves the rule open, and this keeps a broad glob out of every packet.
