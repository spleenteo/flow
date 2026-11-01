# Lessons — doc-territories

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
- An adapter written from the contract alone finds the open points only if its writer lists every guess; turn each into a contract rule and re-align the adapter before any caller relies on it.
- A request key that names a folder (`scratch:`) must say which folder at every call site; "that folder" next to a session folder is read as the session folder.
- A composed file name built from "the file's name" inherits whatever name the caller chose; fix the caller's copy name in the contract, or build the name from the identifier.
- `git diff --quiet [HEAD] -- <path>` exits 0 for an untracked or missing file; guard a "commit it alone" step with `git ls-files --error-unmatch <path>`.
- In a user setup with `defaultMode: auto`, headless runs without a mode flag start in `auto` and `--allowedTools` stops binding; a zero-denial check there proves nothing about the allow-list.
- An executor redoing a commit split must re-stage from its edits, never `git checkout --` them; a lost edit then shows up only as a report claim no commit holds.
- A step that clears state before a remote call (a pending line before `op: archive`) says what happens to that state on each failing return: restore it, or name it in the stop's report.
- A stop inside a closing sequence ("the close resumes at the merge") names who resumes it and how a later session finds it; a `status: closed` work is not routed back by the resume table, so the stopping step's report must name the next move.
- A task reviewer bound by "run no tests or builds" won't run `claude plugin validate .` or the link loop even when the instruction asks; the runner runs them itself at the gate.
- An executor's report lives only in the runner until the ledger write; when a runner can die between a commit and that write, the fix commit's subject (or body) lists each finding it meets, so the resume's review has a record to check against.
- A rule that moves a commit from callee to caller ("the conductor commits the version file") is grepped in every place the callee describes itself, request tables included, not only the lines the plan names; a callee reads its own table.
- A hand-back from a called skill carries every field the caller must write (`proposed <link> (decisions:<name>)`), not just the outcome word; the composed name is seen only by the callee.
- A failure branch in a numbered step says which later steps it skips and where the sequence stops; "report and record nothing" alone lets the next step report success.
- Pick a restore branch by the file's git state (`git ls-files --error-unmatch`), never by a remembered "missing at step N"; a tracked file deleted on disk and an untracked file both defeat the remembered test.
- The Gate's link loop skips only fenced blocks, so a plan that quotes a Markdown link inside inline code (a bracket text followed by a parenthesised target) breaks the gate; quote a link's target without a closing bracket right before an opening parenthesis.
- When a dictated sentence turns a rule into a pointer ("as § X says"), keep the rule's timing at the step ("before the append"); a pointer placed after the action reads as after it.
- A Verified line that names a fixture adapter names its behaviour too: a failure-simulating adapter cannot meet a state comparison of a success path; pick the copying one, or check the failure branch on purpose.
- A base/new difference in a headless run's tool-call shape is rerun once on the side that differed before it is called a slice effect; one run per side does not separate a change from variance.
- When a slice cuts a clause from the conductor as restated contract, check that the contract itself states it; a cut clause the contract never held leaves the behaviour to chance (resolution's `op: none` in Step 1's invocation).
