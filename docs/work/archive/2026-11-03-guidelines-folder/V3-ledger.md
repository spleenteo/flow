# Ledger — V3
printenv CLAUDE_CODE_SUBAGENT_MODEL_FORCE CLAUDE_CODE_SUBAGENT_MODEL: (empty, both unset)

- T1 · flow:executor · definition: sonnet · DONE_WITH_CONCERNS · 569df42
  Status DONE_WITH_CONCERNS; commits 569df42 Add guidelines fields and operations to the territory contract; tests: none (level none), the four Verified commands as expected (2, 1, nothing, § Fields line 149); Servers: none; concerns: "§ Guidelines folder" is SKILL.md's bold-lead paragraph `**Guidelines folder.**` (:52), not a heading (SKILL.md already cites it so); changed lines checked through the Verified outputs and the diff, not one by one; no link check run (no new link).
- T1 review · flow:task-reviewer · opus (top tier, critical; effort high from plan-reviewer.md) · Spec pass, Quality pass · 569df42
  Low: territories.md:168 "no `applies` has none in its `read`" vs a fields table's `applies | null` (add "a row `applies | null` reads as no `applies`"). Low: :180/:192 `list` may filter by `keywords`/`files`, which on `guidelines` would drop `always` documents (T2's rows send a bare `list`). Low: :189 no word on an `applies` value other than globs/always/null (say it is listed as it is). Low, process: changed lines not re-read one by one; no defect found.
- T1: complete

- T2 · flow:executor · definition: sonnet · DONE · b5902f3
  Status DONE; commits b5902f3 Say which guidelines requests each moment sends and in how many invocations; tests: none (level none), Verified: row count 3, `guidelines:<name>` on :100 only (twice on that line), the `/` phrase on :88 (one-invocation paragraph), "On `none`" on :100, "request line per kind" nothing; Servers: none; concerns: none (each changed line re-read).
- T2 review · flow:task-reviewer · opus (top tier, critical; effort high) · Spec pass, Quality fail · b5902f3
  Medium: territories.md:100 the skip "an `applies` neither globs nor `always`" catches `applies: null`, so a literal conductor would skip every document without `applies` (SKILL.md:55 includes them); a plan wording fault. Low: :83/:88 two reuse rules (within a slice vs latest of the session) and :83 restates :88's reuse sentence. Low: :88 "none is made when it decides no read" silent on reads already in scratch. Low: :100 first stop name without SKILL.md's `(n lines)` suffix (left to T4). Cannot verify: whether the opening's `always` reads apply the skips.
- T2 fix round 1 · flow:executor (same, SendMessage) · definition: sonnet · DONE · 6c29ea9
  Status DONE; commits 6c29ea9 Fix review findings on T2: applies null is no applies, not a skip; tests: none (level none), line 100 re-read; Servers: none; concerns: every `applies` mention checked, none contradicts; :191 says nothing of `applies: null`.
- T2 fix review 1 · flow:task-reviewer · opus (top tier; effort high) · Spec pass, Quality pass · 6c29ea9
  Low: :191 tells an adapter when to emit `description: null`, not `applies: null` (T1's text). Low: report form ("none (level none)", "Concerns: none" followed by observations).
- T2: complete

- T3 · flow:executor · definition: sonnet · DONE · fb32210
  Status DONE; commits fb32210 Make an old guidelines adapter stop the run and point Writes at the one exception; tests: none: level none, Verified: pointer count 2, "its one exception" nothing, unmet count 2, "never a document of the folder" :128, decision file exists, link check on territories.md alone nothing; Servers: none; concerns: no "one exception" wording existed to contradict; first `## Tests` Edit missed on backticks, re-applied with a shorter anchor.
- T3 review · flow:task-reviewer · opus (top tier, critical; effort high) · Spec pass, Quality pass · fb32210
  Low: fb32210's subject says "the one exception" while the text says "its exceptions" (two now); not amended. Low: territories.md:48 Stops lead "no work is opened or resumed" vs the new entry's "at any later step" (what a later-step stop leaves is unsaid; rare, resumes resolve again). Cannot verify: pathspec commit form.
- T3: complete

- T4 · flow:executor · definition: sonnet · DONE · eb4e694
  Status DONE; commits eb4e694 Name the adapter path wherever the conductor reads guidelines; tests: none: level none, Verified: 5, 3, `guidelines:<name>` :58 only, no "scheda", First stop and Conventions phrases 1 each; `claude plugin validate .` passes (tail seen: icon, manifest, .DS_Store), link check on SKILL.md nothing; Servers: none; concerns: every old string found; `(42 lines)` matches the existing `guidelines in packet:` form; did NOT read territories.md § Reads/§ Operations against the wordings (lesson V1), nor re-read each changed line (greps and one view of :53–62).
- T4 review · flow:task-reviewer · opus (top tier, critical; effort high) · Spec pass, Quality pass · eb4e694
  Reviewer did the § Reads/§ Operations agreement check itself: all moments and formats match. Medium: SKILL.md:52 "the rules below apply to the `list` lines as to the frontmatter" contradicts the contract's `applies: null` mapping (6c29ea9) and pulls in rules that don't apply to list lines. Medium, process: a Verified item skipped and lines not re-read, reported DONE. Low: :305 "that adapter" may point at the decisions adapter, verbless clause. Low: :197 may read as a new `list`. Low: :52 colon attaches to the wrong clause. Low: :73 Project memory row repo-only (points to § Guidelines folder). Low: :187 decisions clause sits before the decisions item.
- T4 fix round 1 · flow:executor (same, SendMessage) · definition: sonnet · DONE · 43dc25e
  Status DONE; commits 43dc25e Fix review findings on T4: list lines follow the contract's mapping; tests: none: level none, substring count still 5; Servers: none; concerns: :52 re-read; skips compared with territories.md:100, they match.
- T4 fix review 1 · flow:task-reviewer · opus (top tier; effort high) · Spec pass, Quality pass · 43dc25e
  Low: :52 two brackets side by side ("skips) (the contract's ..."). Low: :52 "Otherwise it is read from the checkout", "it" now far from "the repo folder".
- T4: complete

Ruling: T5 (read-through and gate, no commit) gets no task review; its gate runs again at close move 1 and the final review reads the slice whole — a review needs commits to judge and the plan gives T5 none — cost if wrong: a wrong read-through answer slips to the final review, which reads the same file.

- T5 · flow:executor · definition: sonnet · DONE_WITH_CONCERNS · none
  Status DONE_WITH_CONCERNS; commits none; tests: none: level none; read-through answers: `list` :182/:187/:191 (format, `description` last, one line per document, guidelines file not listed, `description: null`, `/` names, `document` reserved), ten-line limit :58; `read` :179/:191/:165/:170; can't list → `unmet: op list on guidelines`, never `none` :191; `none` → nothing read, step goes on :100; `unmet` → run stops, message names adapter and contract change, at resolution or later :48. Exception agrees at :47/:88/:118. Done item 1 holds (:179, :182). Gate: validate passed with § Gate's warnings plus .DS_Store/docs/.DS_Store; old-names rg matches only README § Upgrading from an older Flow (:253–258) and shared/work.md § Upgrade (:392–446), the allowed places; link check with the narrowing glob nothing. Servers: none. Low: :191 doesn't recall the ten-line `results:` rule for a big folder. Low: :179's Returns doesn't mention the named `guidelines` read (stated at :191).
- T5: complete

- Final review · flow:task-reviewer · opus (top tier; effort high) · Spec pass, Quality pass · 569df42 b5902f3 6c29ea9 fb32210 eb4e694 43dc25e
  No high or medium; no logged low rises. Low: territories.md:191 never says when to emit `applies: null`. Low: :48 and SKILL.md:61 Stops wording fits a resolution stop, not "at any later step". Low: :83 vs :88 two reuse rules; :88 silent when every decided read is already in scratch. Low (new): "the guidelines file" used at :74, defined only at :170 (name it in the :23 table). Low: SKILL.md:52 side-by-side brackets, far "it", and the skip mapping restated from the contract's :100 (two copies to keep together). Low: SKILL.md:305 "that adapter", no verb. Low: :197 may read as a new `list`. Low: :187 decisions clause order. Low: :73 Project memory row repo-only. Low: fb32210's subject "the one exception". No fix round: low findings are logged, not fixed.
Final review: done
Gate: green — `claude plugin validate .` passed (warnings: no version ×2, ICON_MISSING, .DS_Store and docs/.DS_Store untracked, marketplace manifest note); old-names rg matches only README § Upgrading from an older Flow (:253–258) and shared/work.md § Upgrade (:392–446); link check with the work's packet narrowing printed nothing.

## Close draft

Narrative: The guidelines territory became multi-document. `shared/territories.md` gained the `applies` field, an `op: list` line format for `guidelines`, `op: read` by name, the `unmet: op list on guidelines` stop at § Resolution (linked to the decision of 2026-11-03), three § Reads rows and the one-invocation exception stated once in § Reads › One invocation per adapter per step, with § Resolution and § Writes pointing at it; `skills/dev/SKILL.md` names the adapter path at every place it reads guidelines. Two review rounds came from one gap: the plan's skip "an `applies` neither globs nor `always`" caught `applies: null`, so T2's round made `applies: null` read as no `applies`, and T4's round rewrote SKILL.md's folder paragraph, which had called the `list` lines equivalent to the frontmatter, to follow the contract's mapping. T5, read-only, got no task review by ruling. The final review passed with lows only, logged (when an adapter emits `applies: null`, the later-step stop wording, the two reuse rules, "the guidelines file" defined late, some SKILL.md phrasing). An adapter serving `guidelines` must now answer `op: list` with one line per document of the folder, `<name> — applies: <globs | always | null>; description: <d>`, and `op: read` with `name: <name>`, while `op: read` with no `name` still returns the guidelines file; one that can't returns `unmet: op list on guidelines`, and Flow stops.
Lessons:
- A plan's skip written as "neither X nor Y" names every value the format allows (`null` included) before it goes to an executor.
- When a fix changes what a value means in the contract (`applies: null`), grep the conductor for the paragraph that maps the same values before the fix passes: SKILL.md:52 restated it and needed its own round.
- An executor that skips a Verified item (the "agrees with § X" read) reports DONE_WITH_CONCERNS, never DONE; say so in the instruction's last line.
- A read-only task with no commit (a read-through, a gate run) says in the plan that it gets no task review, so the runner needs no ruling.
Decision candidate: none
