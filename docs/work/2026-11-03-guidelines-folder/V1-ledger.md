# Ledger — V1
(printenv: CLAUDE_CODE_SUBAGENT_MODEL_FORCE and CLAUDE_CODE_SUBAGENT_MODEL unset)

- T1 · flow:executor · definition: sonnet · DONE · 335788f
  Status: DONE. Commits: 335788f Teach the conductor the guidelines folder and the whole guidelines file. Tests: none: level none (Verified rg checks and plugin validate run, green). Servers: none.
  Concerns: review row says descriptions "not judged again" after the plan (from plan's Selection); folder paragraph states judgment runs at impact and at packet build; :28 Why ends "which travel whole".
Ruling: T1 review lows 4 (broken "plan" resume-row sentence, :209) and 5 (Project memory row understates when frontmatter is read, :86) raised to medium and fixed in round 1; lows 6, 7 fixed alongside as one-word edits in the same lines touched — 4 breaks a sentence a conductor reads, 5 gives a second rule for the same moment, which T1's Done forbids — cost if wrong: a few extra words in one fix commit
- T1 review · flow:task-reviewer · opus (top tier, critical) · Spec fail, Quality fail · medium: design args stated twice (:276/:282); brief's wrong-choice sentence missing; "judged once" with two moments (:55). low: :209 broken sentence; :86 understates reads; :52 "nothing names it"; :43 backticks; status DONE with concerns. cannot verify: plugin validate (gate).
- T1 fix round 1 · flow:executor (same, SendMessage) · definition: sonnet · DONE_WITH_CONCERNS · 398a6ab, 57adaf2
  Status: DONE_WITH_CONCERNS. Commits: 398a6ab Fix review findings on T1: state the design args once, place the wrong-choice rule, tidy rows; 57adaf2 Fix review findings on T1: drop the stray parentheses in the plan resume row. Tests: none (Verified rg + plugin validate re-run, green). Servers: none.
  Concerns: two fix commits (a BSD sed -i failed silently in the first); wrong-choice sentence placed at end of Selection bullet; design/SKILL.md untouched.
- T1 review round 1 · flow:task-reviewer · opus · Spec pass, Quality pass · low: two fix commits instead of one (logged). Out of T1: skills/design/SKILL.md:14 still describes the called-mode args without the guidelines (follow-up).
- T1: complete
- T2 · flow:executor · definition: sonnet · DONE · ec1b866
  Status: DONE. Commits: ec1b866 Name the guidelines folder in the draft and in Step 0. Tests: none: level none ('observed conventions' none; docs/guidelines hits 16 -> 20). Servers: none.
  Concerns: collision clause ends with a semicolon and the folder report before "Then ask:"; the "only those four sections" clause placed after `## Deviations` in The draft.
- T3 · flow:executor · definition: sonnet · DONE · f52d4ad
  Status: DONE. Commits: f52d4ad Send guideline prose to the guidelines folder in the template. Tests: none (four Verified checks pass). Servers: none.
  Concerns: template :7 still mentions `sources:` (left per task; V2's).
- T2 review · flow:task-reviewer · definition: sonnet · Spec pass, Quality pass · No findings.
- T2: complete
- T4 · flow:executor · definition: sonnet · DONE · 0d7d8c3
  Status: DONE. Commits: 0d7d8c3 Name the guidelines folder in the shared work reference and the territories kinds. Tests: none: level none (both Verified greps as expected). Servers: none.
  Concerns: did not read the Guidelines folder paragraph to check agreement; used the task's wording only.
- T3 review · flow:task-reviewer · definition: sonnet · Spec pass, Quality pass · low (cannot verify): commit trailer says Co-Authored-By Claude Opus 5.5; the reviewer's session reminder says Sonnet 5.5 (logged).
- T3: complete
Ruling: T4 review medium (shared/work.md:138 should name development-guidelines.md and the folder) not applied; the plan's folder-only wording stands — slices.md V1 says :138 "says the folder", and in the brief's model the guidelines file keeps four sections (tests, gate, references, deviations) while prose about naming, placement and style goes in the folder, so style preferences live in the folder; this repo's own file still holds Naming/Language until V4 moves them — cost if wrong: one line of shared/work.md reads narrower than a reader expects until V4, fixable in one word
- T4 review · flow:task-reviewer · definition: sonnet · Spec fail, Quality pass · medium: work.md:138 drops the guidelines file (ruled: stands, see Ruling above). low: executor did not read § Guidelines folder before reporting Done.
- T4: complete
Ruling: the gate's link check prints two lines, both from the uncommitted V1-packet.md (its verbatim copy of docs/development-guidelines.md, whose links are relative to docs/), none from a file the slice changed; no fix round run, since the only fixes are editing a verbatim packet or narrowing the gate in slices.md, both outside the runner — the slice's own files are green on validate, old names and links — cost if wrong: the conductor commits a packet that keeps the link check red until the gate excludes *-packet.md or slices.md narrows it
Ruling: final review's three wording lows on skills/dev/SKILL.md (:52 "the template's four sections", :54 the ten-line frontmatter limit unstated, :179 "will be read" overstates) go to the final-review fix executor; its medium (link check red on every committed packet now that the guidelines file travels whole) is the gate matter of the earlier ruling and goes to the user as a STOP, since its fixes (narrowing in slices.md, or a permanent glob in docs/development-guidelines.md § Gate, V4's file) are outside the runner — cost if wrong: one extra fix commit
- Final review · flow:task-reviewer · opus (top tier) · Spec pass, Quality pass · medium: link check red on every committed packet now that the guidelines file travels whole (SKILL.md :42, :56, :306) — to the user. low: :52 "profile's four", :54 ten-line limit unstated, :179 "will be read" overstated (sent to fix); process lows on T1/T4 already logged. cannot verify: ledger reports lack Files: (trimmed copies); validate/old names.
- Final review fix · flow:executor (fresh) · definition: sonnet · DONE · b13d738
  Status: DONE. Commits: b13d738 Fix review findings on V1: say template's sections, the ten-line frontmatter limit, and per-slice choice. Tests: none: level none (rg checks; validate passed, tail read only). Servers: none. Concerns: none.
- Final review fix review · flow:task-reviewer · opus · Spec pass, Quality pass · low: :54 awk never prints the closing `---`, so an unclosed block within ten lines is hard to tell from a closed one (logged; fix: print the marker). cannot verify: full validate warning list (runner ran it: only expected warnings plus .DS_Store, both untracked).
Gate run (runner): validate passes with expected warnings + .DS_Store/docs/.DS_Store (not tracked); old names only in README § Upgrading and shared/work.md § Upgrade; link check prints `docs/work/2026-11-03-guidelines-folder/V1-packet.md -> architecture.md#distribution` and `-> decisions-log/2026-10-25-projects-declare-marketplace-without-ref.md`. Done rg checks: all as expected.
Ruling: user's answer to the gate STOP: (a) — the conductor narrows the link check in slices.md with --glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md' for this work, and V4 makes --glob '!docs/work/**/*-packet.md' permanent in § Gate — packets copy the guidelines file verbatim on purpose, so their relative links are not the repo's — cost if wrong: a real broken link inside a packet goes unseen, which no reader follows anyway
Final review: done
Gate: green — validate passes with the expected warnings (missing version, missing icon, marketplace manifest) plus untracked .DS_Store and docs/.DS_Store; old names only in README § Upgrading and shared/work.md § Upgrade; link check prints nothing with the work's narrowing --glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md'

## Close draft

Narrative: The conductor learned docs/guidelines/: a **Guidelines folder.** paragraph beside § Sources (listing by rg plus one frontmatter awk, the two keys, three selection rules, the ## Guidelines packet section, the guidelines in packet: line at the first stop, the moments without slice paths, the territory condition), and every sentence that picked sections of docs/development-guidelines.md now passes it whole. The draft and Step 0 name the folder; the template sends prose to it; shared/work.md and shared/territories.md follow. T1's critical review found the /flow:design args stated two ways, a brief sentence (a wrong choice fixed by rewriting a description, never by configuration) with no place, and a "judged once" rule naming two moments; one fix round closed them. Ruled: shared/work.md:138 points style preferences at the folder only, as the plan said. The final review found the real cost of passing the guidelines file whole: its relative links break inside every committed packet, so the link check went red; the user chose to narrow the gate for this work, with V4 making the packet exclusion permanent. Left as follow-ups: skills/design/SKILL.md:14 doesn't list the guidelines in the called-mode args; the frontmatter awk should print the closing --- so an unclosed block is visible.
Lessons:
- When a packet starts carrying a whole file, run the link check on the packet too: relative links copied verbatim break from docs/work/<slug>/.
- An instruction that names a moment twice ("once, at impact and at the packet") is two rules: write each moment as its own clause.
- When a plan rewrites how one call's args are built, change the paragraph that defines the call and point to it from elsewhere; never restate the args in a second place.
- An executor whose Done says "agrees with § X" reads § X before reporting; a task-local wording check is not that check.
- On macOS, never edit with sed -i in an executor: it fails silently on BSD sed; use the Edit tool and re-read the changed line.
Decision candidate: none
