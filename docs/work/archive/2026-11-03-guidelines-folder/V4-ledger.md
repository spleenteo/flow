# Ledger — V4
printenv CLAUDE_CODE_SUBAGENT_MODEL_FORCE CLAUDE_CODE_SUBAGENT_MODEL: (empty, neither set)

- T1 · flow:executor · definition: sonnet · DONE · 878cc38
  Status: DONE. Commit: 878cc38 Move naming, language, commits and releases into docs/guidelines. Tests: none (level none); all Verified items run (frontmatter loop empty; applies x4; headings References, Tests, Gate; section diff only trailing blanks and two ../ links; no development-guidelines.md# outside docs/work; link check with narrowing empty; validate passes with expected warnings). Servers: none. Concerns: none.
- T3 · flow:executor · definition: sonnet · DONE · af9007f
  Status: DONE. Commit: af9007f Show a lens is not a rule in the lenses template. Tests: none; validate passes with warnings; Verified three items as expected. Servers: none. Concerns: "next to this file" checked in this repo only (impact-lenses.md in docs/, docs/guidelines/ there); blank line added between the new comment and the Optional comment.
- T1 review · flow:task-reviewer · opus (top tier, critical; effort high from plan-reviewer.md) · spec pass, quality pass · no findings
  Note (not a finding): docs/briefs/2026-11-03-guidelines-folder.md:82 still says "development-guidelines.md § Releases" in plain text; briefs are records, not edited, like the 2026-10-25 decision in the slice's Out.
- T1: complete
- T3 review · flow:task-reviewer · definition: sonnet · spec pass, quality pass
  Low: impact-lenses.template.md:3 "next to this file" points nowhere when guidelines is on a territory and lenses is not (siblings otherwise, SKILL.md:166,171); "in the project's guidelines files" would hold in every case. Low: the new comment is prose, parsed by nothing; SKILL.md cites the template only by path.
- T3: complete
- T2 · flow:executor · definition: sonnet · DONE · efd0969
  Status: DONE. Commit: efd0969 Make the link check skip packets for good. Tests: none; three Verified items as expected (glob count 1; no packet in rg --files; sentence at line 26). Gate link check as written, no extra narrowing: empty, V4-packet.md skipped. Servers: none. Concerns: none.
- T4 · flow:executor · definition: sonnet · DONE (should have been DONE_WITH_CONCERNS: heading order not confirmed by output) · dc74b90
  Status: DONE. Commit: dc74b90 Rewrite Customizing a stack around the guidelines folder. Tests: none; link check on README.md empty. Verified: removed strings print nothing; tree lines 174-176; "the guidelines file" one hit at :259 (§ Upgrading, untouched); headings order not seen by the executor, confirmed by the runner: 108 Customizing a stack, 116 Several files, 129 Which option, 139 Replacing a preset. Checked against SKILL.md § Guidelines folder (keys, packet contents, first stop, always files at resume). Servers: none. Concerns: none.
- T5 · flow:executor · definition: sonnet · DONE · 7982ce2
  Status: DONE. Commit: 7982ce2 Show the guidelines folder in the architecture map and the site tree. Tests: none. Verified: site guidelines/ count 1; gdir at 155, 166, 175, 181; row at :197, Read by cell same as SKILL.md:73 plus the file's name; "the guidelines that apply" and "naming, placement" gone; link check on architecture.md empty; mermaid checked by eye, not rendered; site spans at column 0 after decisions-log/. Servers: none. Concerns: none.
- T2 review · flow:task-reviewer · opus (top tier, critical; effort high from plan-reviewer.md) · spec pass, quality pass · no findings
  Checked: glob covers work-dir and archived packets; scratch packets are outside the repo; no other copy of the § Gate command exists.
- T2: complete
- T4 review · flow:task-reviewer · definition: sonnet · spec pass, quality pass · no findings
  Checked against SKILL.md:52, 54, 72, 187; Rails examples agree with the tree; first-stop ruling held (README says docs/development-guidelines.md in every line T4 wrote).
- T4: complete
- T5 review · flow:task-reviewer · definition: sonnet · spec pass, quality pass
  Low: architecture.md:197 Written by says the upgrade moves "a sources: file"; only path entries with globs move, and not when the destination is taken or frontmatter would run past line 10 (shared/work.md:409). Acceptable for a map. Mermaid not rendered; parse judged by reading.
- T5: complete
- T6 · flow:executor · definition: sonnet · DONE · no commit (read-only; no task review, per plan)
  Status: DONE. Frontmatter loop empty; headings References, Tests, Gate; guidelines/ hits in README (89, 114, 118, 133, 174, 208, 259, 276), architecture (57, 133, 155, 197), site (193); development-guidelines.md# only in this work's own documents; validate passes with § Gate's warnings plus two .DS_Store metadata warnings; old names: only README § Upgrading and shared/work.md § Upgrade; link check as written: empty. Read-through: stack guidelines README:112, mine README:114, several files README:116-128, in order. Mermaid block rendered with mermaid-cli in the scratchpad: parses. Concerns: none.
- T6: complete
- Final review · flow:task-reviewer · opus (top tier; effort high from plan-reviewer.md) · spec pass, quality pass · shas 878cc38 af9007f efd0969 dc74b90 7982ce2
  Low 1: README.md:114 and :127 say a file is chosen by its description even when its applies globs don't match; SKILL.md:56 judges the description only for files with no applies. Low 2: development-guidelines.md:8 "travel with them" has no clear subject. Low 3: T4's executor reported DONE without seeing one Verified output (V3 lesson not yet followed); runner confirmed; lesson for the close. No cross-file contradiction; no text citation of the moved sections outside records; every In covered, nothing from Out touched.
- Final review fix · flow:executor · definition: sonnet · DONE · 3fbd762
  Status: DONE. Commit: 3fbd762 Fix review findings on V4 final review: guideline selection wording. README:114 and :127 match SKILL.md:56; development-guidelines.md:8 names naming.md and language.md, all four applies: always; no other copy of the old rule; link check empty. Servers: none. Concerns: none.
- Final review fix review · flow:task-reviewer · definition: sonnet · spec pass, quality pass · no findings
  Note: README:127 "with a description only" reads as "no applies", made clear by the rails/deploy.md example.
- Gate run by the runner from the repo root: validate passed with § Gate's warnings plus .DS_Store and docs/.DS_Store metadata (untracked: git ls-files prints nothing); old names only in README § Upgrading and shared/work.md § Upgrade; link check as written in § Gate (packet glob permanent, no work narrowing): empty. Done 1-4 rerun after 3fbd762: frontmatter loop empty; headings References, Tests, Gate; guidelines/ hits README 8, architecture 4, site 1; development-guidelines.md# only in this work's slices.md, ledger, packet. No server started; lsof shows only system listeners.
pending: With a clone of this repo in the scratchpad at this branch and `claude --plugin-dir <clone>`, `/flow:dev` on a throwaway repo that copies this repo's `docs/development-guidelines.md` and `docs/guidelines/` reaches a first stop whose guidelines line names `commits.md`, `language.md`, `naming.md` and `releases.md` with line counts.
Final review: done
Gate: green (validate passes with expected warnings; old names: allowed matches only; links: none broken)

## Close draft

Narrative: Five commits and one read-only check, no ruling. T1 moved Naming, Language, Commits and Releases into four `docs/guidelines/` files with `applies: always`, byte for byte apart from the two `../` links, added `## References` before `## Tests`, and repointed the README and architecture links in the same commit, so no commit left a link to a moved section. T2 made the link check skip `docs/work/**/*-packet.md` for good, which ends V1's narrowing; the reviewer found no other copy of the gate command. T3 gave the lenses template its two examples and the rule-vs-lens line. T4 rewrote "Customizing a stack" around the folder. T5 added the folder to the architecture map, with its mermaid block rendered to confirm it parses, and to the site tree. T6's read-through found the developer's three questions answered in order (README:112, :114, :116-128). Every task review passed with no high or medium finding. The final review found that README:114 and :127 claimed a description chooses a file even when its globs don't match, which SKILL.md:56 rules out. The sentence predated V4 but now answered the developer's third question, so one fix round (3fbd762) aligned it, and also cleared up "travel with them" in References. T4's executor reported DONE without seeing one Verified output; the runner confirmed the heading order itself. The `--plugin-dir` first stop is pending, with the user. Briefs and the 2026-10-25 decision still name "§ Releases" in plain text; both are records and are not edited.
Lessons:
- When the runner finds an executor "judged" a Verified item rather than seeing its output, it runs the item itself before the review and logs the report as DONE_WITH_CONCERNS; the V3 rule alone did not stop it.
- When a slice rewrites a README section around a rule, every sentence left in that section that restates the rule is checked against the conductor's paragraph, old sentences included: README:127 predated V4 and contradicted SKILL.md:56.
- A changed mermaid block is rendered with `npx -y @mermaid-js/mermaid-cli` into the scratchpad before the close; reading by eye is not a parse check.
Decision candidate: none
