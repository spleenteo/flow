---
work: doc-territories
slice: V1
updated: 2026-10-31
tags: [work, doc-territories, plan]
description: "Plan of V1: the changelog key accepts a map (path, labels, language, decisions layout) and changelog-propose finds dated work folders"
---

# V1 — The changelog adapts to the project — Plan

**Mandate**: [V1-packet.md](V1-packet.md) · **Guidelines**: [development-guidelines.md](../../development-guidelines.md) · **Lessons**: none yet (no `lessons.md`) · **Execution rules**: [executor.md](../../../agents/executor.md)

**Summary**
1. T1 teaches `flow:docs` the map form of `changelog:` (path, labels, language, decisions layout), with defaults equal to today's entry, and the `rg`-based lookup of the work folder.
2. T2 makes `shared/work.md` read the changelog path from either form (keys line, archive step 2).
3. T3 documents the map in the key list of `skills/dev/SKILL.md`, the README and `docs/architecture.md`; T4 runs the Done items in a throwaway repo in the scratchpad; nothing from it is committed.

**Files**: skills/docs/SKILL.md, shared/work.md, skills/dev/SKILL.md, README.md, docs/architecture.md

## The slice, decided here

- `<changelog>` in `skills/docs/SKILL.md` means the changelog's path from here on: the string value, or the map's `path`, default `<root>/CHANGELOG.md`. The `search` and commit lines keep the placeholder, now defined, rather than each restating the two forms.
- The map's keys and defaults: `path` (`<root>/CHANGELOG.md`), `labels.work` (`Work`), `labels.decision` (`Decision`), `language` (unset: today's rule, existing entries, then the rest of `<root>/`), `decisions` (`lines`). A missing key takes its default; unknown keys are ignored. Flow-style on one line and block style are both accepted.
- `decisions` holding anything but `lines` or `inline` → `FAILED: changelog.decisions must be lines or inline`, nothing written. It's a file value, so `NEEDS` (which makes the conductor ask the user and resend) wouldn't help.
- The entry template block in `skills/docs/SKILL.md` stays untouched: it is the default. The rules say how labels and `inline` change it. That is what keeps the default byte-for-byte.
- `language` sets the prose of the entry (and the intro of a new changelog), not the labels: with `language: it` and no `labels`, the bullets still read `**Work**` and `**Decision**`. It doesn't touch the commit message, which follows the history's language as today.
- `inline`: one bullet, the label, then every link joined by ` · ` (space, U+00B7, space). With one decision, `inline` and `lines` give the same line.
- Work folder: `rg -l '^work: <slug>$' <dir>/<root>/work/*/STATUS.md`. The `*/` level never reaches `<root>/work/archive/<x>/`, so the wall holds. No match → continue from the git facts alone, as today when the folder is missing. More than one match → `FAILED: several work folders for <slug>`. `changelog-write` uses the same lookup for the plans and `STATUS.md` it scans for `manual after merge`, replacing `<dir-of-work>`.
- `shared/work.md` line 8, "Keys this file uses", gains `changelog`: archive step 2 reads it. It's the same reader, so the slice already covers it.

## Tasks

### T1 — `flow:docs` reads the changelog map and finds dated work folders · critical

**What**: in `skills/docs/SKILL.md`:
- the `changelog` row of § Configuration (~:67) becomes "a path, or a map; see below", default `<root>/CHANGELOG.md`, and is followed by a short paragraph listing the map's keys, defaults, accepted styles, the `decisions` FAILED rule, and the sentence defining `<changelog>` as the path;
- **Language** (~:73): `language` first when set, then today's two rules;
- `changelog-propose` step 2 (~:87): "Read the changelog" names `<changelog>`, and the work's `STATUS.md` and `slices.md` are read in the folder the lookup finds, with its no-match and several-matches rules;
- § Rules of `changelog-write` (~:133-136): **Work** says its label is `labels.work`; **Decision** says its label is `labels.decision`, and `lines` gives one bullet per decision as in the template while `inline` gives one bullet joining the links with ` · `; **No operational instructions** names the folder found by the lookup in place of `<dir-of-work>`;
- `search` (~:170): `<dir>/<changelog>` gets "(the changelog's path)".

**Files**: skills/docs/SKILL.md
**Verified**:
- `rg -n "rg -l '\^work: <slug>\\$' <dir>/<root>/work/\*/STATUS.md" skills/docs/SKILL.md` → at least one line.
- `rg -n 'dir-of-work|\*-<slug>' skills/docs/SKILL.md` → no output.
- `rg -n '<root>/work/<work>/' skills/docs/SKILL.md` → no output (the undated lookup is gone).
- `rg -n 'labels\.work|labels\.decision|decisions: lines|inline' skills/docs/SKILL.md` → matches in § Configuration and § Rules.
- `git diff main -- skills/docs/SKILL.md` → no `-` or `+` line between the ```` ```markdown ```` fence of § Entry and its closing fence.
- `claude plugin validate .` → passes with the expected warnings only.

**Done**: every reader of the key in `flow:docs` (configuration, language, propose, write rules, search) handles both forms, and the default entry is today's. **Interfaces**: produces the map's key names and defaults, the meaning of `<changelog>`, and the lookup command. T2, T3 and T4 use them word for word.

### T2 — The work reference reads the changelog path from either form · depends on: T1

**What**: in `shared/work.md`, line 8 adds `changelog` to "Keys this file uses" with "a path, or a map whose `path` is used; see `flow:docs` § Configuration". Archive step 2 (~:303) defines `<changelog>` as that path, default `<root>/CHANGELOG.md`, before `rg "<slug>" <changelog>`.
**Files**: shared/work.md (plus any of the read-through files that contradicts T1)
**Verified**: `rg -n 'changelog' shared/work.md` → line 8 names `changelog`; the step 2 line names the map's `path` and the default `<root>/CHANGELOG.md`. Read-through against the new `flow:docs` wording of the files that cite it: `skills/changelog/SKILL.md:18`, `skills/archive/SKILL.md:21`, `shared/work.md:218-235`, `skills/docs/decisions.md:120,141`; fix any that now contradicts it, else report them as checked.
**Done**: `/flow:archive` finds the entry whichever form `changelog:` takes.

### T3 — The key list, the README and the architecture document the map · depends on: T1

**What**:
- `skills/dev/SKILL.md` ~:49: `changelog` becomes "`changelog` (a path, or a map `{path, labels: {work, decision}, language, decisions: lines | inline}`; the entry rules are `flow:docs`'s)".
- `README.md` ~:172: keep `changelog: docs/CHANGELOG.md` and add, after the YAML block, one sentence with the map example `changelog: {path: docs/CHANGELOG.md, labels: {work: Job, decision: Why}, language: it, decisions: inline}` and what each key changes.
- `docs/architecture.md` ~:196: the `.flow.yml` row says `changelog` is a path or a map (`path`, `labels`, `language`, `decisions`).
- Use the key names from T1 word for word, invented values only.

**Files**: skills/dev/SKILL.md, README.md, docs/architecture.md
**Verified**: `rg -n 'decisions: lines \| inline|decisions: inline|`decisions`' skills/dev/SKILL.md README.md docs/architecture.md` → one match or more in each file.
**Done**: a reader of any of the three learns the map form and where its rules live.

### T4 — Run the Done items in a throwaway repo · depends on: T1

**What**: build the repo in `/private/tmp/claude-501/-Users-spleenteo-Sites-me-flow/a55fc672-afea-4698-96d9-3664e936b88f/scratchpad/v1-repo` (`$S` below). Never inside this repo; nothing committed here. Setup, on `main`: `.version` holding `0.1.0`; `docs/CHANGELOG.md` with an English two-line intro; one commit. Then on branch `work/menu`: `docs/work/2026-10-01-menu/STATUS.md` (frontmatter `work: menu`, `base: main`) and `slices.md` with `## V1 — done on 2026-10-02` saying the menu collapses below 640px (a fact found only there; commit subjects stay neutral, e.g. "Add menu"); `docs/work/2026-10-01-mobile-menu/STATUS.md` (`work: mobile-menu`) and `slices.md` about a dark mode toggle; `docs/decisions-log/2026-10-02-menu-collapses.md` and `2026-10-02-menu-icon.md`, each with `work: menu` in its frontmatter; `src/menu.js`; one commit. Copy `$S` to `$S-string` (`.flow.yml` holding `changelog: docs/CHANGELOG.md`) and `$S-none` (no `.flow.yml`), then write `$S/.flow.yml` with `changelog: {labels: {work: Job, decision: Why}, decisions: inline, language: it}`. Commit `.flow.yml` in each copy that has one. Each run:

```bash
(cd "$R" && claude -p --plugin-dir /Users/spleenteo/Sites/me/flow --permission-mode acceptEdits \
  --allowedTools "Skill Read Write Edit Bash(git:*) Bash(rg:*)" \
  "Invoke the Skill tool with skill flow:docs and args: <request line>. Then print the skill's answer verbatim and nothing else.")
```

1. `$S`: `op: changelog-propose, layout: repo, dir: $S, work: menu, base: main, release: one`.
2. `$S`, `$S-string` and `$S-none`: `op: changelog-write, layout: repo, dir: <R>, work: menu, base: main, release: one, level: minor, title: "<title>"`. Use an Italian title in `$S` and an English one in the copies. A permission denial or a failed launch → stop and report it (status `BLOCKED`, with the output); never change the permission mode. The conductor then runs T4 in the main session after the user's ok.
3. Baseline: `git -C /Users/spleenteo/Sites/me/flow archive main | tar -x -C <scratchpad>/flow-main` (create the folder first), copy `$S-none` afresh to `$S-base`, and run the same `changelog-write` as in `$S-none` with `--plugin-dir <scratchpad>/flow-main`.

**Files**: none (scratchpad only; no commit)
**Verified**:
- `rg -l '^work: menu$' $S/docs/work/*/STATUS.md` → only `.../2026-10-01-menu/STATUS.md`.
- Run 1 → one line `minor → 0.2.0: <title> — <reason>` whose title or reason reflects the 640px collapse (proof the right folder was read), with no mention of dark mode.
- Run 2 in `$S` → `Recorded v0.2.0 — …`. In `$S/docs/CHANGELOG.md`: `rg -c '^- \*\*Job\*\*: `menu`$'` → 1; `rg -c '^- \*\*Why\*\*: '` → 1, and that line holds both decision file names joined by ` · `; `rg -c '\*\*Work\*\*|\*\*Decision\*\*'` → 0; the prose is Italian on a read.
- Run 2 in `$S-string` and `$S-none` → each entry has `## v0.2.0 — <today> — <title>`, `- **Work**: `menu``, then two separate `- **Decision**:` lines linking `decisions-log/2026-10-02-…` files, then a blank line and `---`. That is the template of § Entry with no other structure.
- Baseline: `diff <(rg '^(## v|- \*\*(Work|Decision)\*\*|---$)' $S-none/docs/CHANGELOG.md | sed 's/ — .*— / — X — /') <(rg '^(## v|- \*\*(Work|Decision)\*\*|---$)' $S-base/docs/CHANGELOG.md | sed 's/ — .*— / — X — /')` → no output (titles normalised; same structure as today's Flow).

**Done**: the four Done items of V1, short of the Gate, hold. Output and paths go in the report.
## Order

T1 first. Then T2, T3 and T4 side by side: separate files, and T4 writes only in the scratchpad. The Gate follows all four.

## Deviations from the mandate

- The mandate's lookup `rg -l '^work: <slug>$' <root>/work/*/STATUS.md` is written with the `<dir>/` prefix, as `flow:docs` requires for every `rg` path. It also gets two rules the mandate leaves open: no match continues from git, several matches fail.
- `shared/work.md` line 8, "Keys this file uses", is edited besides archive step 2: it lists the keys the file reads, and step 2 reads `changelog`.
