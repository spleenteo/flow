# Audit: documentation territories (main → 84231e7)

Read-only audit of `git diff main 84231e7 -- . ':!docs/work'`. Line numbers are at 84231e7. Categories: 1 inconsistency, 2 contradiction, 3 repetition, 4 dead end, 5 hole. Each finding was checked against the frozen text; findings I could not confirm were dropped.

Counts: **high 3 · medium 11 · low 12** (26 findings).

Files read in full at 84231e7: shared/territories.md, shared/work.md, skills/dev/SKILL.md, skills/docs/SKILL.md, skills/archive/SKILL.md, skills/grill/SKILL.md, skills/changelog/SKILL.md, agents/slice-runner.md, README.md (config section), docs/architecture.md (diff), skills/dev/development-guidelines.template.md, docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md, skills/docs/scripts/decisions-check, skills/design/SKILL.md (§ adapter resolution), docs/work/2026-10-31-doc-territories/{shaping,slices}.md for rationale.

---

## High

### H1 · [5 hole] Changelog on a territory: the version is committed before the adapter write, and a failed write leaves a bumped version with no entry anywhere

- `shared/territories.md:134`, `shared/work.md:236-237`, `skills/docs/SKILL.md:156-160, 185-189`
- What: with `scratch`, `changelog-write` writes the entry to `<scratch>/CHANGELOG.md`, writes the version file and **commits it alone** (`Bump the version to vX.Y.Z`). Only then does the conductor send `op: write; kind: changelog`. `work.md:237` handles the adapter's answer as `written` / `proposed` / "`FAILED: …` is reported and added nowhere"; `unmet` and `unreachable` on this write are not mentioned at all.
- Why it matters: on any adapter failure the repo now holds a version bump commit on the branch while the entry exists only in a `mktemp -d` folder that dies with the session. Nothing says to undo the commit or to keep the scratch file. A re-run of `/flow:changelog` reads the bumped version file and proposes the *next* version (`docs:115`: "The current number: the version file"), so the release is numbered twice. This is the one path in the change that leaves committed state inconsistent on a failure.
- Fix: with `scratch`, `changelog-write` writes the version file but does **not** commit (return `Composed … ; version: <path>` with no sha, like `decision-write`). The conductor commits `<version_file>` alone only after the adapter returns `written` or `proposed`; on `unmet`/`unreachable`/`FAILED` it runs `git -C <dir> checkout -- <version_file>`, reports the adapter's line and the scratch `CHANGELOG.md` path, and nothing is recorded. Alternatively keep the commit order and add an explicit undo (`git revert --no-edit HEAD` guarded by the subject) to `work.md:237`.

### H2 · [4 dead end] "The close resumes at the merge" after a `proposed` archive, but no path can get there

- `skills/dev/SKILL.md:388`, `shared/work.md:239, 365`
- What: on `proposed`, "the close stops before the merge: the user runs `/flow:archive` again on the work's branch once the proposal is accepted, and the close resumes at the merge." `/flow:dev`'s resume (`dev:181`) offers only works "with `phase:` other than `done`"; a closed work has `phase: done` (`work.md:223`). `/flow:archive` never merges (`archive/SKILL.md`), and its report (`work.md:365`) names no next move.
- Why it matters: the user is told the close resumes, and nothing resumes it. The merge, push, tag and the final report (which is where the pending-proposals line is supposed to appear, `dev:390`) are silently dropped. Listed as a V7 item in `slices.md`, so it is known, but at 84231e7 the text promises a path that does not exist.
- Fix (V7): either (a) `/flow:dev` On start also lists closed works whose `STATUS.md` holds a `— archive:<dir> — ` line (or whose folder is still present with `status: closed`) and routes a yes to Step 6 move 4; or (b) the `/flow:archive` report on a territory ends with the merge commands of § Closing a work step 4 and says the report is the user's. Until then, change `dev:388`/`work.md:239` to say what actually happens.

### H3 · [5 hole] Archive re-run: the pending line is cleared and committed *before* `op: archive`, so a failed re-run loses the only pointer to the open proposal

- `shared/work.md:349` (step 3) vs `:352` (step 6); `shared/territories.md:138`
- What: step 3 removes the `— archive:<dir> — <link>` line from `STATUS.md` and commits `Clear the pending proposal on archive`, so that the folder matches the accepted container. Step 6 then sends `op: archive`; on `unmet`, `unreachable` or `FAILED:` "the archive stops, with nothing removed" — but the pending line was already removed and committed, and the proposal link is gone from the repo. Also, because step 3 always clears first, the contract's branch "unless `STATUS.md` already holds `— archive:<dir> — `, in which case the append is skipped" (`territories.md:138`) can never be taken on a re-run: a premature re-run (proposal not yet accepted) produces a clear commit followed by a fresh `Note a pending proposal on archive` commit every time.
- Why it matters: the pending line is the work's record of where the proposal lives; losing it on a transient `unreachable` means the user has to find the proposal by hand. V7 lists the restore, confirming the gap.
- Fix (V7): on `unmet`/`unreachable`/`FAILED:` after a clear, re-append the same line and commit `Note a pending proposal on archive` again (or `git revert` the clear commit), and say so in the report. Drop the dead "unless already holds" clause from `territories.md:138` or re-word it for the by-hand case where no clear happened.

---

## Medium

### M1 · [5 hole] `unmet`, `unreachable` and non-`exists` `FAILED:` on `op: write` are undefined mid-work

- `shared/territories.md:140` (Returns), `:48` (Stops), `shared/work.md:172, 174`, `skills/dev/SKILL.md:147`
- What: the Returns paragraph handles `written`, `proposed <link>` and `FAILED: <id> exists`. The only other rule is § Resolution's stop ("no work is opened or resumed"), which is meaningless at a slice close or a definition-phase close where a decision is being recorded. For a decision `write`, a status-change `write` and the `## Tests` `write`, nothing says what the conductor reports or keeps when the adapter answers `unreachable: …` or `FAILED: <other reason>`. The composed file sits in `mktemp -d` and is lost with the session.
- Fix: add to Returns: "`unmet: …`, `unreachable: …` or any other `FAILED: <reason>`: the write is not recorded; report the adapter's line and the composed file's absolute path, add nothing to `STATUS.md`, and do not end the run; the user may ask to resend." Mirror in `work.md:172`.

### M2 · [5 hole] `read` returning `missing` for `guidelines` or `lenses` is unhandled, and the `## Tests` write then has no document to go into

- `shared/territories.md:99, 136, 186`, `skills/dev/SKILL.md:145, 149`
- What: `op: read` may return `missing` (`:186`). For `product`, `missing` is covered (`:109` "Undeclared or `missing`: nothing"). For `guidelines`: `:99` says Tests and Gate "come from its `read`: no branch lookup, no local file", `dev:145` forbids a draft, template or missing-sections proposal, and `:136` writes the two `## Tests` lines "into the conductor's copy of the guidelines `read`" — with `missing` there is no copy, so a project whose guidelines territory is still empty can never get its level recorded. `lenses` `missing` is likewise unstated (presumably "project lenses: none", but nothing says so).
- Fix: in § Reads › Guidelines on a territory: "`missing`: there are no guidelines; the profile asks the level, and the `## Tests` write sends a new file holding only `## Tests` with the two lines (`op: write`, no `name`, which creates the single document at `where`)." In § Reads: "`lenses` `missing`: as an absent `impact-lenses.md` (project lenses: none)."

### M3 · [4 dead end] Step- and phase-scoped territory sources have no read moment, and their scratch copies have no rebuild rule

- `skills/dev/SKILL.md:57, 60, 328`, `shared/territories.md:81, 83, 107`, `agents/slice-runner.md:22`
- What: `dev:57` allows `{territory: <kind>, name: <name>}` in place of `path` with any `when`, including `{step: execute}`, `{step: close}` or `{phase: shaping}`. The contract's Reads table reads "a matched source naming a territory" only at impact, the slice packet, resume at `step: plan` and after the plan (rows 81, 83): all path-scoped moments. No row reads a step- or phase-scoped territory source, so `Execute sources:`/`Close sources:` (which must be absolute paths of scratch copies, `:107`) and the phase blocks (`dev:60`) have nothing to point at. On a resume in a fresh session the scratch copies are gone; `dev:328` rebuilds only the packet.
- Fix: either restrict `territory` sources to glob `when` values (and say an entry with `step:`/`phase:` is "skipped and named" like an unknown `when`), or add Reads rows for `step: plan|review` (before the dispatch), `step: execute|close` (before move 4, in the same invocation as the packet reads) and each phase (with the product read), and extend `dev:328`'s rebuild to the `Execute sources:`/`Close sources:` copies.

### M4 · [2 contradiction] `/flow:grill` must not commit when called, yet § Decisions › On a territory commits the pending line

- `skills/grill/SKILL.md:38, 42, 46`, `shared/work.md:172`, `shared/territories.md:142`
- What: `grill:38`: "Called: the caller commits on the work branch. By hand: write, don't commit, and say so." `grill:42` then sends a called or by-hand grill through § Decisions › On a territory, which on `proposed` "inside a work" appends the line to `STATUS.md` and commits it alone (`Note a pending proposal on decisions`). A called grill has `work: <slug>` (inside a work), so it would commit mid-dialogue, against its own rule. Its return (`grill:46`) carries no `proposed <link>` either, so a caller that is supposed to own the commit cannot write the line itself.
- Fix: in `grill:42`: "Called: hand the adapter's `written <id>` / `proposed <link>` back in the decision-candidates list; the caller appends the pending line and commits. By hand: report the link only." Add `proposed <link>` to the return format at `:46`.

### M5 · [4 dead end] Pending proposals for `decisions`, `changelog` and `guidelines` are never resolved, so the close reports accepted ones as pending

- `shared/territories.md:140-142`, `skills/dev/SKILL.md:390`, `shared/work.md:254, 349`
- What: lines under `## Pending proposals` are read in two places only: the work close's report (verbatim) and archive step 3 (kept for the report). No step checks whether a proposal has since been accepted (the contract gives the means: a `read`/`list` that now returns the document, `:198`). Requirement R4 (`shaping.md`) says the close lists "the proposals still pending"; the text lists every line ever appended, and an archived work carries stale lines into the territory.
- Fix: at the close's report (and optionally at resume), for each pending line send the kind's `op: read` (`name` = the line's `<name>`; `op: list` for `archive`) in the step's invocation; a document that now exists is reported as "accepted" and its line removed with the close commit. Or state plainly that the list is "as recorded, not re-checked".

### M6 · [3 repetition] The conductor grew 23% and restates the contract in several places

- `skills/dev/SKILL.md` 8,566 → 10,503 words (396 → 412 lines, but lines are paragraphs); `dev:65, 212, 217, 244, 271`
- What: (a) `dev:65` § Territories repeats the contract's stop list, opening-line format and changelog-path rule. (b) The product-block procedure ("one adapter invocation with `op: read; kind: product` and `op: none` lines… copied to the scratch folder… never read the territory's files directly") and the verbatim sentence ``Context only: cite it as `product:<name>`…`` appear twice in dev (`:244`, `:271`) and once in the contract (`territories.md:109`). (c) The anchored match ``rg '(— |; )work: <slug>(;|$)' <file> | awk '{print $1}'`` appears five times (`territories.md:134, 201`, `work.md:228, 347`, `dev:212`). (d) The pending-line append-and-commit procedure appears four times (`territories.md:140-142`, `work.md:172, 237, 352`). (e) "`labels`, `language` and `decisions` still apply" ×3 (`territories.md:36`, `dev:65`, `README:186`). (f) "A missing `<changelog>` path is left out of `search`'s `rg` call" (`territories.md:95`) duplicates `docs:198`.
- Why: every copy is a drift point; the first drift already exists (M9). The conductor's own rule is "stays thin".
- Fix: keep the command and the pending-line procedure in the contract only and cite "§ Writes › The pending line" / "the contract's anchored match"; collapse `dev:244` and `:271` to one sentence pointing at `territories.md` § Reads › Product; cut the restated stop list from `dev:65`.

### M7 · [4 dead end] "Read its `## Requires`, then invoke it" cannot be done by name, and the adapter already checks it

- `skills/dev/SKILL.md:65`, `shared/territories.md:46, 60`, `skills/design/SKILL.md:44, 53`
- What: `dev:65` orders "read its `## Requires`, then invoke it with the Skill tool by its exact qualified name". The conductor has no path to another plugin's skill file and the contract forbids invoking by path (`:46`); the design pattern it borrows reads Requires *after* the skill resolves, i.e. once the Skill tool has loaded its body (`design:53`). The contract also makes the adapter run its own `Requires` on every op, `op: none` included (`:60, 64`), so the conductor's read is redundant.
- Fix: replace with "invoke it with the Skill tool…; the adapter checks its own `## Requires` and answers `ready` or `unmet: <what>`".

### M8 · [4 dead end] The slice runner cannot tell a territory source from a repo file

- `agents/slice-runner.md:29`, `shared/territories.md:107`, `skills/dev/SKILL.md:328`
- What: the runner is told to head a territory source `### <kind>:<name>` "which has no path", but it receives only absolute paths in `Execute sources:`/`Close sources:` (`:107`: "the runner gets the path of its copy in the scratch folder"). Nothing tells it which paths are territory copies or how to recover `<kind>` and `<name>` (derivable from `<scratch>/<kind>/<name>.md` only if the layout is stated).
- Fix: pass territory sources as `<kind>:<name>=<absolute path>` in the two fields (and say so in `slice-runner.md:22`), or state the `<scratch>/<kind>/<name>.md` layout in the runner's instruction.

### M9 · [1 inconsistency] Whether a `write` request carries the `write` word is stated three ways

- `shared/territories.md:56, 126, 130` vs `:132, 134, 136`; `shared/work.md:174, 237`
- What: `:56` and `:126` say every request carries `write` when declared, and the decision write at `:130` shows `[; write: <word>]`. The status-change write (`:132`), the changelog write (`:134`) and the guidelines write (`:136`) omit it, while `work.md:174` and `:237` include it for the same requests.
- Fix: add `[; write: <word>]` to `:132, 134, 136`, or drop the brackets everywhere and rely on `:56`.

### M10 · [5 hole] A background runner that cannot read the scratch packet has no defined return

- `skills/dev/SKILL.md:327-335`, `shared/territories.md:111`, `slices.md` V5 narrative (spike)
- What: the packet goes to `mktemp -d` outside the repo whenever `decisions`, `guidelines` or `lenses` is declared. The V5 spike recorded that a background subagent's `Read` of that folder is denied headless in default mode and "expected to prompt" interactively. `dev:335` handles `NO_NESTING` and a dispatch "denied or erroring before the runner starts"; a runner that starts and is denied the packet read has no return value and no fallback.
- Fix: in `slice-runner.md` § What you do: "If the `Packet:` file cannot be read, return `STOP` with `Question: packet unreadable at <path>` and nothing written"; in `dev:335`: on that `STOP`, run `#### The loop` in session (the packet is already in context) or re-dispatch with the packet inline.

### M11 · [1 inconsistency] `op: list` on decisions at the close is filed under Writes, not Reads

- `shared/territories.md:121` vs `:85`
- What: the Writes table has "Close, changelog step | `decisions` | `op: list`, when `decisions` is declared (for the ids)"; the Reads table lists only the changelog `read` for that moment. A reader scanning Reads for "what the close fetches" misses the list.
- Fix: move the row to the Reads table (and keep the one-invocation note at `:134`).

---

## Low

### L1 · [2] Default-path change: the archive's changelog check is now a whole-line fixed-string match

- `shared/work.md:309` (was `rg "<slug>" <changelog>` on main)
- What: ``rg -n -F -x -e '- **<label>**: `<slug>`'`` requires the exact bullet; an adopted changelog whose Work bullets differ (trailing space, older label) now reports "recorded nowhere" where it didn't before. Non-blocking (the step asks), but "unconfigured = unchanged" is bent.
- Fix: on no anchored match, fall back to the loose match and say which matched.

### L2 · [2] "Only repo reads before territories" contradicts stack detection

- `skills/dev/SKILL.md:104` vs `:41`
- What: the bold rule allows only the `stacks:` line, the upgrade check and `.flow.yml`, yet the same sentence says "else detection", which reads `package.json`, `Gemfile`, `astro.config.*`. Also, the dependency check now reads `stacks:` from the checkout only, while the profile reads it on branches (`:133`): a `stacks:` committed only on a work branch is missed by the dependency check (it was already ambiguous on main).
- Fix: "no `docs/` file" is the real rule; say that, and let detection read its signals.

### L3 · [5] The anchored match interpolates `<slug>` into a regex and assumes whitespace-free identifiers

- `shared/territories.md:134, 201`, `shared/work.md:228, 347`, `skills/dev/SKILL.md:212`
- What: `rg '(— |; )work: <slug>(;|$)'` breaks on a slug with regex metacharacters (the user may rename the proposed slug), and `awk '{print $1}'` returns a truncated id when an adapter's identifier holds a space; the contract never says identifiers are one token.
- Fix: add to § Operations: "identifiers hold no whitespace or `;`"; or use `awk -F' — ' -v s='<slug>' '$0 ~ "(^|; )work: " s "(;|$)" {print $1}'` with `s` escaped.

### L4 · [1] The archive identifier is `<dir>`/`<name>` in the contract and `<id>` in the conductor

- `shared/territories.md:138, 190, 197` vs `skills/dev/SKILL.md:212` ("a match names `archive:<id>`")

### L5 · [1] architecture.md still says `/flow:archive` adds `archived:` before the move, unqualified

- `docs/architecture.md:189` vs `shared/work.md:345` ("no `archived:` marker" on a territory)

### L6 · [1] README's `sources` paragraph omits the `{territory, name}` form

- `README.md:184` ("`path` is repo-relative and must be a tracked file") vs `skills/dev/SKILL.md:57`

### L7 · [1] "`list` lines carry `status` and `work` only, with `description` last" names three fields

- `shared/territories.md:194`; also `:197` repeats the form. Reword: "`status`, `work` and `description` only".

### L8 · [5] With a changelog territory, `flow:docs` `search` still greps a stale repo changelog

- `skills/docs/SKILL.md:73, 195, 198`, `shared/territories.md:36`
- What: `flow:docs` does not read `territories:`; `search` greps `<dir>/<changelog>` whenever that file exists. A repo `CHANGELOG.md` left behind when the changelog moved to a territory is searched beside the territory's excerpts.
- Fix: the conductor passes `changelog: none` (or the contract tells the user to remove the repo file when declaring the territory).

### L9 · [4] A rebuilt-packet `Ruling:` line may precede the ledger head

- `skills/dev/SKILL.md:328`, `shared/work.md:212-214`
- What: on a resume whose first runner died before the head write, the conductor writes `Ruling:` "to the ledger before move 4"; the loop then writes the head "unless the ledger already has it", so the head lands after the ruling, breaking "first line `# Ledger — V<n>`".
- Fix: "if the ledger does not exist, write the head (with `Packet:`) and the ruling in one write".

### L10 · [1] Product read: "reused this session" vs "build the block just before the invocation"

- `shared/territories.md:87` vs `skills/dev/SKILL.md:271`. Say which: one read per session for `product`, or one per phase.

### L11 · [5] The archive commits sweep anything already staged

- `shared/work.md:336, 361`: `git commit -m "Archive the <slug> work"` with no pathspec after `git add <files>`; `git rm` staged the removal. Pre-existing pattern on main; the territory path adds the rewrite files. Fix: `git commit -m … -- <root>/work/<dir> <files with updated links>`.

### L12 · [2] The removal guard checks `status: closed` but not `phase: done`

- `shared/work.md:352, 356` vs `:266` ("if the frontmatter doesn't say `status: closed` and `phase: done`, say so and ask"). Add `grep -qx 'phase: done'` to both guards.

---

## Confirmed sound (checked, no finding)

- Unconfigured path: `docs:77` map defaults reproduce today's entry; `search`/`descriptions` without `excerpts` behave as before; `changelog-propose`'s dated-folder lookup is a fix, not a change (R8.2); packet place and runner fields are unchanged without `territories:`/`sources:`.
- Scratch paths: every `mktemp -d` is outside the repo; nothing in the diff writes a territory path or a token into a committed file except `<where>` in the archive's plain-text rewrite (`work.md:360`), which V7 already fences ("`where` must never carry a token or a share id").
- Archive ordering is copy-then-remove with guards (`work.md:352, 356`), and the way back is given; `decisions-check` accepts a non-`.md` `superseded_by` identifier (`decisions-check:103-105`), so territory ids pass.
- The `## Tests` `proposed` on a brand-new work is covered by the untracked/missing `STATUS.md` clause (`territories.md:142`).
