# Ledger — V8
printenv CLAUDE_CODE_SUBAGENT_MODEL_FORCE CLAUDE_CODE_SUBAGENT_MODEL: (empty, neither set)

- T1 · flow:executor · definition: sonnet · DONE · 207d81c
  Status: DONE. Commits: 207d81c Give the anchored match, the product read and the changelog skip one home in the territory contract. Tests: level none; forbidden literals no output; seven phrases 1 each; awk on $S/v8-t1/blk.txt printed `2026-10-31-cart-total` then `2026 10 31 spaced id` only; link loop empty. Servers: none. Concerns: none (edits by $S/v8-t1-edit.py, each target asserted unique).
- T2 · flow:executor · definition: sonnet · DONE · b5dce52
  Status: DONE. Commits: b5dce52 Point the work reference at the contract's rules and guard the archive commits. Tests: level none; forbidden literals none; phase: done 2, anchored match ( 2, NEXT 1; $S/v8-t2a commit R100 + M old STATUS + A archived.md, notes stays staged; $S/v8-t2b D lines + M README.md, notes staged; guard nothing/ok; version restore tracked → committed value, untracked → removed, porcelain clean; L1 exact none, loose prints; link loop empty. Servers: none. Concerns: a hook blocked `rm`, reran with `mv` (repos $S/v8-t2a–d, $S/v8-t2c-gone); (f) pathspec includes docs/work/<dir> whose removal is staged, confirmed accepted by git.
- T3 · flow:executor · definition: sonnet · DONE · a3a05f4
  Status: DONE. Commits: a3a05f4 Let flow:docs skip a stale repo changelog on search. Tests: repo-changelog 2; validate warnings only. Servers: none. Concerns: none.
- T4 · flow:executor · definition: sonnet · DONE · 168fc7d
  Status: DONE. Commits: 168fc7d Align README and architecture with the territory contract. Tests: both rg 1; link loop empty. Servers: none. Concerns: none.
- T5 · flow:executor · definition: sonnet · DONE · c74b624
  Status: DONE (report lost: the previous runner died on ENOTFOUND; recorded from the commit). Commits: c74b624 Point the conductor's configuration, memory and profile at the territory contract. Tests: not reported. Servers: not reported. Concerns: report lost; reviewed from the commit.
- T3 · flow:task-reviewer · definition: sonnet · pass (spec pass, quality pass) · a3a05f4
  No findings. Row at skills/docs/SKILL.md:42, sentence at :194 word for word; one file; commit rules met.
- T3: complete
- T1 · flow:task-reviewer · opus (effort high) · pass (spec pass, quality pass) · 207d81c
  (a)–(f) exact; awk byte-identical to the plan's L3; seven phrases 1 each on the committed blob; fixture read by hand gives the two identifiers. Low: shared/territories.md:110 "just before the invocation" follows "one adapter invocation" and can be read as the adapter call (plan's dictated text). Low: :76 says "matched on `work` anchored (§ Operations)" rather than naming **The anchored match**. Low: the trimmed report has no `Files:` line (commit shows one file).
- T1: complete
- T4 · flow:task-reviewer · definition: sonnet · pass (spec pass, quality pass) · 168fc7d
  No findings. Both inserts exact, two files only, commit rules met.
- T4: complete
- T5 · flow:task-reviewer · opus (effort high) · pass (spec pass, quality pass) · c74b624
  Verified checked by reading: forbidden phrases none, five phrases at :65, :81, :104, :174, :57; wc -w 10703 < 10909; link loop empty; every pointer's section exists at HEAD; obligations kept at their steps. Low: :65 "says when a `changelog` territory takes the place" reads as timing. Low: :59 "one `### <path>` per source" no longer names the territory heading. Low: :174 doesn't tie each offer to its kind. Cannot verify: report lost.
- T5: complete
- T6 · flow:executor · definition: sonnet · DONE · 0ae4c1c
  Status: DONE. Commits: 0ae4c1c Point the conductor's Step 1 to Step 6 at the territory contract. Tests: level none; forbidden literals none; GRILL 3; § Reads › Product 2; four single counts 1 each; wc -w 10438 (≤ 10450); validate warnings only; link loop empty. Servers: none. Concerns: none (did not check that each cited section exists).
- T2 · flow:task-reviewer · opus (effort high) · fail (medium) · b5dce52
  Spec pass, quality pass, one medium: shared/work.md:172 the pointer to § Writes › The pending line came after the append, reading as add-then-check while the contract's `git diff --quiet HEAD` check only passes before the append (plan's dictated text). Low: :309 loose L1 match counts a longer slug's hit; low: :335 `docs/work/archive` pathspec commits unrelated unstaged edits in archived works; low: $S/v8-t2c holds a stray `S` fixture file.
- T2 fix round 1 · flow:executor (fresh: original unreachable after the runner died) · definition: sonnet · DONE_WITH_CONCERNS · 9a4e012
  Status: DONE_WITH_CONCERNS. Commits: 9a4e012 Fix review findings on T2: run the pending-line check before the append. Tests: level none; forbidden literals none; new phrase 1; link loop not run (runner ran it: empty). Servers: none. Concerns: also reworded :237 changelog path to "the check run before the append and the commit as in § Decisions › **On a territory**".
- T2 · flow:task-reviewer · opus (effort high) · pass (spec pass, quality pass) · 9a4e012
  :172 word for word; :237 within remit; T2 Verified counts hold (anchored match ( 2, phase: done 2, NEXT 1). Low: report's "second rg returned 1" misstates (file gives 2 for `anchored match (`). Low: :172 sentence still placed after the append instruction (says "before the append"). Low: :237 construction awkward.
- T2: complete
- T6 · flow:task-reviewer · opus (effort high) · pass (spec pass, quality pass) · 0ae4c1c
  (a)–(i) word for word; GRILL identical at :216, :245, :274; every pointer's section exists at HEAD; obligations at their steps; counts 3, 2, 1×4; wc -w 10438. Low: shared/work.md:254 step 5 no longer names `STATUS.md`'s `## Pending proposals` as the source while the folder is in the repo. Low: skills/dev/SKILL.md:245 product read lost its "when it writes `frame.md` itself" scope (plan's text). Low: :388 cites "§ Closing a work (repo), step 2" and "§ Closing a work, step 2, item 7" in two forms.
- T6: complete
- T7 · flow:executor · definition: sonnet · DONE · none (scratchpad only)
  Status: DONE. Commits: none. Files: $S/v8-moved.tsv. Tests: Verified loop printed nothing (no `missing:`); SKILL.md diff from 70d0400: 25 removed, 27 added lines; wc -w 10909 → 10438. Servers: none. Concerns: padding spaces stripped on two double-backtick strings.
- T7 · flow:task-reviewer · definition: sonnet · pass (spec pass, quality pass) · none
  TSV matches the plan one for one (30 strings, not 31: a counting slip); padding strip has no effect; each home is real. Low: count stated as 31.
- T7: complete
- T8 · flow:executor · definition: sonnet · DONE_WITH_CONCERNS · none (scratchpad only)
  Status: DONE_WITH_CONCERNS. Commits: none. Files: $S/v8-t8-results.txt, $S/v8-run.sh, $S/v8-build.sh, $S/v8-heads-before.txt. Tests: 8 runs, permissionMode auto, -a base 70d0400, -b HEAD 0ae4c1c, stderr empty. Openings: porcelain clean, HEAD/refs unchanged, stores unchanged in both. v8-open pairs differ: -b adds `op: none; kind: archive` before `op: search`/`op: list` (both have those two); excerpts: 1/1; L3 `archive:2026-10-31-cart` 2/2. v8-opennone: 0 adapter calls, 0 mktemp, both. v8-arcnone: same subject, count 1, name-status identical in -a/-b, holds the old work's M line (plus A .claude/settings.json, M decision, A archived.md, R cart files). v8-arc: $S/v7-adapter returns `FAILED: store offline` on archive, both stop clean, nothing committed (Verified unmet in both alike); supplementary v8-arc6 pair with $S/v6-adapter: Archive the cart work, count 1, name-status and stores identical. Servers: none. Concerns: v7-adapter is the failure simulator; the extra op: none in -b is the one base/new difference; nothing denied or flagged.
- T8 · flow:task-reviewer · definition: sonnet · fail (medium) · none
  Medium: v8-open pair sets differ (-b adds a separate `op: none; kind: archive`), departing from shared/territories.md:64, :88 (op: none only for kinds with no read in the step); harmless on state; possibly variance; fix: rerun -b once. Low: scratch folder location differs (-a under $S, -b /var/folders), not a violation. v8-arc substitution by v8-arc6 accepted; report should say so plainly.
- T8 fix round 1 · flow:executor (same, SendMessage) · definition: sonnet · DONE · none
  Status: DONE. Commits: none. Files: $S/v8-open-b2 (+ store, pristine), results appended to $S/v8-t8-results.txt. Tests: HEAD 2481a2e, auto, plugin $S/v8-flow; one demo-territory:folder call with op: search and op: list kind archive, no op: none; porcelain, HEAD, refs, store unchanged; archive:2026-10-31-cart 2; excerpts: present. Results file names v8-arc6 as meeting the archive Verified. Servers: none. Concerns: 1 of 2 new-flow runs sent op: none, 0 of 1 base.
- T8 · flow:task-reviewer · definition: sonnet · pass (spec pass, quality pass) · none
  Rerun's claims checked against jsonl, store and repo. Low: results note states "was variance" as fact; should read "not reproduced in one rerun; treated as variance".
- T8: complete
Ruling: T8's extra `op: none; kind: archive` in v8-open-b is ruled model variance, not a slice effect — a rerun on the new flow sent the contract's shape (one invocation, search and list, no op: none), state was unchanged in every run, and at HEAD the conductor no longer says "op: none for each declared kind" — cost if wrong: one redundant adapter call per opening with an archive territory, no state harm; a later headless run would show it again.
Ruling: T8's archive check is met through the v8-arc6 pair ($S/v6-adapter) in place of v8-arc — the plan named $S/v7-adapter, which fails `op: archive` by design, so v8-arc stopped clean in both trees alike — cost if wrong: none for the shared path; the failure branch was compared too (both stop, nothing committed).
- Final review · flow:task-reviewer · opus (effort high) · pass (spec pass, quality pass) · 207d81c b5dce52 a3a05f4 168fc7d c74b624 9a4e012 0ae4c1c aacdb93
  M6 restatements each have one home and pointers to existing sections; L1–L12 and V7's four lows fixed where named; callees (docs, grill, changelog, archive, slice-runner) agree; unconfigured path unchanged. No high or medium. Low: skills/dev/SKILL.md:218 `repo-changelog: skip`'s condition only in a parenthesis (:81 template unconditioned), not exercised by T8. Low: shared/work.md:228 standalone /flow:changelog reaches the anchored match only through a parenthesis and never says to read the contract first (as :347 does). Low: dev:65 lost the clause putting resolution's op: none "together with the step's reads … in one invocation", so T8's extra op: none is left open by the text rather than pure noise; fix in territories.md § Resolution. Process: T5's report lost, T1's trimmed report has no Files: line. Cannot verify: gate and validate (reviewer runs neither). Lows logged, not fixed; no fix round.
- Gate fix round · flow:executor · definition: sonnet · DONE · aacdb93
  Status: DONE. Commits: aacdb93 Fix the gate on V8: unlink the plan's quoted link targets. Tests: Gate link loop empty; only V8-ledger.md and V8-packet.md untracked. Servers: none. Concerns: none. (Failure: link loop printed V8-plan.md -> ../../shared/territories.md and -> ../../2026-10-31-cart/STATUS.md, quoted text at T5 (d) and T8.)
- Gate fix · flow:task-reviewer · definition: sonnet · pass (spec pass, quality pass) · aacdb93
  Two spots only, meaning kept; runner reran the link loop: empty.
Final review: done
Gate: green (validate passed with the expected warnings; old names only in shared/work.md § Upgrade and README § Upgrading from an older Flow; link loop empty after aacdb93)

## Close draft

Narrative: The conductor shrank from 10909 to 10438 words. The anchored match (rewritten for L3 to compare `work` exactly, never inside `description`, keeping identifiers with spaces), the product read and block, the pending-line check and the repo-changelog skip each have one home in `shared/territories.md` or `shared/work.md`, and `skills/dev/SKILL.md` points at them; T7 found every removed rule in its new home. The audit's lows L1–L12 and V7's four lows were fixed in their files, including the archive commits' pathspecs, the `phase: done` guard and a version restore picked by `git ls-files`. The runner died on a network error after T5's commit; on resume T1–T5 were reviewed from their commits, with T5's report lost. T2's review found the plan's pointer sentence for the pending line placed after the append, reading as add-then-check; one fix put "before the append" back at the step. The gate went red on the plan itself: quoted Markdown links in inline code (T5 (d), T8) are read by the link loop; one fix unlinked them. T8's headless pairs matched on state; the plan's `v7-adapter` fails `op: archive` by design, so the archive comparison ran on the copying `v6-adapter` (ruled). One new-flow opening sent an extra `op: none; kind: archive`; a rerun sent the contract's shape, so it was ruled variance, though the final review notes that V8 dropped the clause binding resolution's `op: none` to Step 1's invocation, so § Resolution should settle it. Final review passed with lows only: `repo-changelog: skip`'s condition and the standalone changelog's anchored match sit in parentheses.
Lessons:
- The Gate's link loop skips only fenced blocks, so a plan that quotes a Markdown link inside inline code (`[x](../y)`) breaks the gate; quote a link's target without the `](` form.
- When a dictated sentence turns a rule into a pointer ("as § X says"), keep the rule's timing at the step ("before the append"); a pointer placed after the action reads as after it.
- A Verified line that names a fixture adapter names its behaviour too: a failure-simulating adapter cannot meet a state comparison of a success path; pick the copying one, or check the failure branch on purpose.
- A base/new difference in a headless run's tool-call shape is rerun once on the side that differed before it is called a slice effect; one run per side does not separate a change from variance.
- When a slice cuts a clause from the conductor as restated contract, check that the contract itself states it; a cut clause the contract never held leaves the behaviour to chance (resolution's `op: none` in Step 1's invocation).
Decision candidate: none
