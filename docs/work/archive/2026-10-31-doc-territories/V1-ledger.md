# Ledger — V1
printenv CLAUDE_CODE_SUBAGENT_MODEL_FORCE CLAUDE_CODE_SUBAGENT_MODEL: (empty, neither set)

- T1 · flow:executor · definition: sonnet · DONE · 3ad0257
  Status: DONE. Commits: 3ad0257 Teach flow:docs the changelog map and the dated work folder lookup. Tests: level none; Verified lines run (lookup rg line 89; no dir-of-work / *-<slug> / <root>/work/<work>/; labels matches at 73, 135-136; no diff inside § Entry template; plugin validate passes). Servers: none. Concerns: none (avoided the literal "*-<slug>" wording because Verified forbids it).
- T1 review · flow:task-reviewer · opus (top tier, critical) · Spec pass, Quality pass · 3ad0257
  Low (logged, not fixed): (1) skills/docs/SKILL.md:89 lookup has no `2>/dev/null`; an empty `work/` glob errors under zsh/bash and may be read as failure rather than "no match". (2) :135 "the work's slug in `<root>/work/`" still suggests slug = folder name. (3) report over 20 lines because of the requested Interfaces block.
- T1: complete

- T2 · flow:executor · definition: sonnet · DONE · 9f3db72
  Status: DONE. Commits: 9f3db72 Read the changelog path from either form in the work reference. Tests: level none; rg 'changelog' read back. Servers: none. Concerns: none. Read-through, no change: skills/changelog/SKILL.md:18, skills/archive/SKILL.md:21, shared/work.md:218-235, skills/docs/decisions.md:120,141.

- T3 · flow:executor · definition: sonnet · DONE · 7e9a93b, d8dec3b
  Status: DONE. Commits: 7e9a93b Document the changelog map in the key list, README and architecture; d8dec3b Fix backticks in the README changelog paragraph. Tests: level none; Verified rg matches in all three files. Servers: none. Concerns: two commits (second a typo fix on own README edit); full gate not run.
- T2 review · flow:task-reviewer · definition: sonnet · Spec pass, Quality pass · 9f3db72
  Low (logged): shared/work.md:303 has no note on a missing or non-string `changelog` value; T1's rule covers it.
- T2: complete
- T3 review · flow:task-reviewer · definition: sonnet · Spec pass, Quality pass · 7e9a93b, d8dec3b
  Low (logged): two commits where executor.md asks one per task; 7e9a93b ships a stray-backtick README line fixed by d8dec3b (no amend). Gate not run by the task; confirm at close.
- T3: complete

- T4 · flow:executor · definition: sonnet · DONE · none (scratchpad only)
  Status: DONE. Commits: none. Tests: ran the skill in scratchpad repos v1-repo, v1-repo-string, v1-repo-none, v1-repo-base (main's Flow). Lookup → only 2026-10-01-menu; propose → "minor → 0.2.0: Il menu si comprime ... sotto i 640px — ..." (no dark mode); write in $S → one **Job** line, one **Why** line joining both decisions with " · ", 0 Work/Decision, Italian prose; $S-string and $S-none → today's template; normalised diff $S-none vs $S-base empty. Servers: none. Concerns: `rm` blocked by a hook (no cleanup); `--allowedTools` swallowed the prompt argument, so the prompt went on stdin; permission mode unchanged; entry prose varies between runs, structure matches.
- T4 review · flow:task-reviewer · definition: sonnet · Spec pass, Quality pass · none
  Re-ran lookup, counts, entry structure and baseline diff in the scratchpad: all match; repo untouched. Low (logged): Run 1 propose output not re-run (report only); .flow.yml commit in v1-repo-string not checked; decision link text varies between runs (model prose).
- T4: complete

- Final review · flow:task-reviewer · opus (top tier) · Spec pass, Quality pass · 3ad0257 9f3db72 7e9a93b d8dec3b
  No high or medium; no fix round. Low (logged): (1) skills/docs/SKILL.md:172 "`<dir>/<changelog>` (the changelog's path)" is slightly off and repeats :73; (2) :73 definition of `<changelog>` omits the default `<root>/CHANGELOG.md` that shared/work.md:303 states; (3) :73 the `decisions` FAILED rule says "nothing written" and doesn't say changelog-propose fails too; (4) d8dec3b lacks the attribution trailers. Outside the slice: `rg "<slug>" <changelog>` (shared/work.md:303, skills/docs/SKILL.md:135) matches `mobile-menu` for `menu`; search ``rg -F '`<slug>`'`` in a later slice.
Final review: done
Gate: green (plugin validate passed with the expected warnings, plus .DS_Store warnings for local gitignored files not in git; old names only in README § Upgrading and shared/work.md § Upgrade; link check empty)

## Close draft

Narrative: The changelog key now takes a path or a map (`path`, `labels.work`, `labels.decision`, `language`, `decisions: lines | inline`); `flow:docs` defines `<changelog>` once and every reader (shared/work.md archive step 2, the dev key list, README, architecture) points to it. The work folder is found by `rg -l '^work: <slug>$' <dir>/<root>/work/*/STATUS.md`, with "no match: git facts only" and "several: FAILED". Run in scratchpad repos, the map config gave an Italian entry with one `**Job**` and one `**Why**` line joining two decisions with ` · `, propose for `menu` read only its own dated folder (640px fact, no dark mode), and the string and no-key configs matched main's Flow structure line for line. The entry template was left untouched, which is what kept the default identical. All four tasks passed on the first review; no rulings. Left low for later: the lookup has no `2>/dev/null` for an empty `work/`, `<changelog>`'s sentence at :73 omits its default, and archive's `rg "<slug>" <changelog>` still matches `mobile-menu` for `menu` (the same prefix bug, in the entry search).
Lessons:
- In `claude -p`, `--allowedTools "<list>"` is variadic and swallows a prompt argument after it; pipe the prompt on stdin.
- A Verified line that forbids a literal (`rg -n '\*-<slug>'` → no output) also forbids quoting it in prose; plan the check to match only the command, or word the prose without it.
- A slug lookup by substring (`rg "<slug>"`, `*-<slug>`) matches longer slugs; anchor it on the exact field (`^work: <slug>$`, ``-F '`<slug>`'``).
- A skill's glob over `<root>/work/*/` gets `2>/dev/null`, as shared/work.md does, so an empty folder reads as no match.
Decision candidate: none
