# V4 packet — guidelines-folder

Work: technical (no frame, no criterion). Repo root: /Users/spleenteo/Sites/me/flow. Branch: work/guidelines-folder.

## Slices.md — header (mandate, shared gotchas, gate narrowing)


Mandate: Work 1 of [`docs/briefs/2026-11-03-guidelines-folder.md`](../../briefs/2026-11-03-guidelines-folder.md) ("What changes", Goals, Non-goals, Anti-goals, Calls, Left to the builder). The project is a plugin of Markdown skills: a slice is "done" when the text says what the brief says, the gate of `docs/development-guidelines.md` is green, and a read-through finds no skill contradicting another.

Order: V1 → V2 → V3 → V4. V1 adds the folder while `sources:` still exists; V2 removes `sources:` and adds its upgrade; V3 moves the folder onto a territory; V4 makes this repo and its docs the first user.

Impact (2026-11-03) ran two agents on these slices; its 23 findings are folded into the items below. No slice was split or reordered.

Shared gotchas:

- `skills/dev/SKILL.md` is ~400 lines and cites itself by section names (`§ Sources`, `§ Presets`, `Profile › The draft`). Other files carry their own strings: `shared/work.md:208` cites `§ Sources`; `agents/slice-runner.md` carries `Execute sources:` and `Close sources:`; `shared/territories.md` has "Sources naming a territory" and "a matched source names a territory". Renaming or removing one means fixing every citation: `rg -n -i '§ Sources|Execute sources|Close sources|matched source|Sources naming' skills shared agents` before and after.
- The gate's old-names check and link check run from the repo root (`docs/development-guidelines.md` § Gate). A new relative link must resolve; a template's placeholder links sit inside fenced blocks. The link check strips anchors (`${t%%#*}`): a dead `#section` passes green, so anchors are checked by hand.
- `claude plugin validate .` passes with the warnings § Gate lists (missing version, missing icon, the folder also holding a marketplace manifest) plus OS-metadata warnings for files `git ls-files` doesn't print (`.DS_Store`); anything else is a finding.
- The conductor's rule "until § Territories resolves, no `docs/` file is read" covers the new folder: the folder is read after territories, with the scheda.
- Packet paths are expanded absolute paths, never `${CLAUDE_SKILL_DIR}` (the work reference, § Agents › Dispatch contract).
- This session runs the installed plugin copy (0.5.0), not this clone. A Done item that needs the edited conductor to run uses a clone of this repo in the session scratchpad with `claude --plugin-dir <clone>` on a throwaway repo, never this checkout and never a branch created here (`docs/development-guidelines.md` § Tests and § Releases).

**Gate narrowing for this work** (ruled at V1's close, 2026-11-04): the link check of § Gate runs with `--glob '!docs/work/2026-11-03-guidelines-folder/*-packet.md'` added to its `rg --files` call. A packet copies `docs/development-guidelines.md` whole, so its relative links never resolve from the work folder. V4 makes `--glob '!docs/work/**/*-packet.md'` permanent in § Gate, and this narrowing ends with the work.

---

## Slice section

## V4 — This repo adopts the folder; the docs show it

**In.**

- `docs/development-guidelines.md` keeps `## Tests`, `## Gate`, `## References` (added, naming the brief, `docs/guidelines/releases.md` and `docs/guidelines/commits.md` by path, and the skills) and `## Deviations` (only if there is content); `## Naming`, `## Language`, `## Commits` and `## Releases` become files under `docs/guidelines/` (`naming.md`, `language.md`, `commits.md`, `releases.md`), each with a `description` and `applies: always` (the naming rules bind every document, the README and the site included; every merge is a release), text unchanged except relative links, which gain one `../`.
- Inbound references to the moved sections: `README.md:278` ("the commit rules: `docs/development-guidelines.md`") and the `development-guidelines.md#releases` anchors in `README.md` (§ Develop the plugin) and `docs/architecture.md:57` point at `docs/guidelines/releases.md` and `commits.md`.
- `skills/dev/impact-lenses.template.md`: two example lenses in its comments (multi-tenant ids; a DatoCMS sandbox that costs money), one line on the difference from a rule, and `:3` says coding rules go in the guidelines folder.
- `README.md`: the "Customizing a stack" section rewritten around the folder: where a stack's rules live (preset), where yours live (the folder, two frontmatter lines), one example with three Rails files, how to replace a preset. Short: Work 2 will cut the README further. Also `README.md:89` ("the guidelines that apply"), `:122` and `:208` ("naming and placement follow `docs/development-guidelines.md`") say the folder, and the project tree (`:164-189`) gains `guidelines/`. `docs/architecture.md`: `docs/guidelines/` row in the "What a project holds" table and node in its diagram; the scheda's row says what it holds now; `:133` ("the guidelines that apply") says the scheda and the folder. `site/index.html`'s project tree gains `guidelines/`.
- `docs/development-guidelines.md` § Gate: the link check's `rg --files` call gains `--glob '!docs/work/**/*-packet.md'` permanently (packets copy the scheda whole, so their relative links never resolve), with one sentence saying why; the narrowing V1 set for this work ends.
- `docs/CHANGELOG.md`: nothing here; the close writes it through `flow:docs`.

**Out.** The README's full rewrite and `docs/configuration.md` (Work 2). The site's text beyond the tree. `docs/design/flow_home-page.pen` (the site's design file holds the old tree; it follows in Work 2). The decision of 2026-10-25 (`projects-declare-marketplace-without-ref.md:28`) says "the guidelines' `## Releases` rule carries this": decisions aren't edited; known and accepted.

**Done.**

- `for f in docs/guidelines/*.md; do head -1 "$f" | grep -qx -- '---' && grep -q '^description:' "$f" || echo "bad: $f"; done` prints nothing.
- `rg -n '^## ' docs/development-guidelines.md` shows Tests, Gate, References and, at most, Deviations.
- Gate green from the repo root: validate, old names, links; the link check prints nothing for `docs/guidelines/`; `rg -n 'development-guidelines.md#' --glob '!docs/work/archive/**' .` shows no anchor to a moved section.
- `rg -n 'guidelines/' README.md docs/architecture.md site/index.html` hits each.
- With a clone of this repo in the scratchpad at this branch and `claude --plugin-dir <clone>`, `/flow:dev` on a throwaway repo that copies this repo's `docs/development-guidelines.md` and `docs/guidelines/` reaches a first stop whose guidelines line names `commits.md`, `language.md`, `naming.md` and `releases.md` with line counts.
- Read-through of the README section as the external developer of the brief: the three questions he asked (where the stack's guidelines live, where mine go, how I give several files) are answered in that order.

**Gotchas.** Until this release is installed, the conductor running this work is 0.5.0 and reads the scheda only: from V4's commit on, the close reads `docs/guidelines/releases.md` and `commits.md` by hand before the merge, with a note in `STATUS.md`'s Log. The site's tree is inside a `<pre>`: keep the indentation.

## Lessons


One rule per line, read by every later subagent.

## From V1

- When a packet starts carrying a whole file, run the link check on the packet too: relative links copied verbatim break from docs/work/<slug>/.
- An instruction that names a moment twice ("once, at impact and at the packet") is two rules: write each moment as its own clause.
- When a plan rewrites how one call's args are built, change the paragraph that defines the call and point to it from elsewhere; never restate the args in a second place.
- An executor whose Done says "agrees with § X" reads § X before reporting; a task-local wording check is not that check.
- On macOS, never edit with sed -i in an executor: it fails silently on BSD sed; use the Edit tool and re-read the changed line.

## From V2

- The link check doesn't skip inline code: never write a literal close-bracket-paren pair in prose or backticks; say "a Markdown link".
- A review fix that adds a rule of its own (a limit, an exception) gets checked against every place the same rule is stated (SKILL.md's frontmatter limit, the conversion paragraph, the README copy) before it passes.
- When one paragraph is copied verbatim into another file, every fix to the rule it summarises edits both copies in the same commit and compares the bytes.
- A fix-round instruction that rewords a phrase another file cites (T4's "line-only") names the citing file, so both change together.
- Shell in a skill (greps, awk) is run in a scratchpad throwaway on each edge case it names (comment in frontmatter, no frontmatter, unclosed block) before the review, not after.

## From V3

- A plan's skip written as "neither X nor Y" names every value the format allows (`null` included) before it goes to an executor.
- When a fix changes what a value means in the contract (`applies: null`), grep the conductor for the paragraph that maps the same values before the fix passes: SKILL.md:52 restated it and needed its own round.
- An executor that skips a Verified item (the "agrees with § X" read) reports DONE_WITH_CONCERNS, never DONE; say so in the instruction's last line.
- A read-only task with no commit (a read-through, a gate run) says in the plan that it gets no task review, so the runner needs no ruling.

## Test level

none (project Level: none)

## Guidelines (docs/development-guidelines.md, all sections apply)

Rules the plans and the changes in this repo must follow. The repo is a Claude Code plugin made of Markdown skills: there is no build and no test framework.

### Tests

Level: none
Sensitive areas:

No test framework. A slice is verified by running what it changed:

- `claude plugin validate .` for anything under `.claude-plugin/` or `skills/`;
- a throwaway test repo, created outside this repo (in the session scratchpad or `/tmp`), for loading, naming and settings behaviour. Never committed;
- for a change to a skill's instructions, a read-through against the brief and the skills that cite it.

Every behaviour of Claude Code a document states is either verified in a spike or a slice, or linked to the official docs page that says it.

### Naming

- A skill's folder is its `name:`, kebab-case: `skills/dev/SKILL.md` has `name: dev`.
- User-invocable skills are cited as `/flow:<skill>` in every document. Skills that aren't user-invocable (`flow:docs`, `flow:stack-<name>`, `flow:design-<tool>`) and agents (`flow:planner`, …) are cited as `flow:<name>`, with no slash. Dependency skills are cited by bare name, with the plugin form in brackets at the first mention: `shaping` (`shaping-skills:shaping`). The brackets go only at a dependency's first mention in each skill, bare afterwards.
- No names of specific user projects or personal tools anywhere. Examples use invented names.

### Language

English in every file, commit message and work document.

### Commits

Imperative mood, English, one concern per commit. Explicit `git add <paths>`, never `-A` or `.`. No `CLAUDE.md` at the repo root (`claude plugin validate` warns on it).

### Gate

Green before a slice closes, in order, from the repo root. The link check skips links inside fenced code blocks, where templates hold placeholder links. Both `rg` calls name the path (`.`): without one, `rg` reads stdin when it isn't a terminal and hangs.

```bash
claude plugin validate .        # passes; expected warnings: missing version and icon, the folder also holding a marketplace manifest
# old names: the only allowed matches are in shared/work.md § Upgrade and in the README's
# § Upgrading from an older Flow; nothing else.
# --hidden so .claude-plugin/ is searched too
rg -n -i --hidden '\bdevflow\b|devflow-(docs|archive)|projectflow|\.devflow\.yml' \
  --glob '!.git' --glob '!.git/**' \
  --glob '!docs/work/**' --glob '!docs/briefs/**' --glob '!docs/CHANGELOG.md' \
  --glob '!docs/development-guidelines.md' .
# every relative Markdown link resolves: prints "file -> target" per broken link, nothing when green.
# Add --glob '!path' to the rg --files call for the narrowing a work's slices.md sets.
rg --files --glob '*.md' --glob '!docs/work/archive/**' . | while read -r f; do
  awk '/^ *```/{x=!x; next} !x{s=$0; while(match(s,/\]\([^) ]+/)){print substr(s,RSTART+2,RLENGTH-2); s=substr(s,RSTART+RLENGTH)}}' "$f" |
    while read -r t; do
      case "$t" in http*|mailto:*|\#*) continue;; esac
      test -e "$(dirname "$f")/${t%%#*}" || echo "$f -> $t"
    done
done
```

While a work is in progress, its `slices.md` may narrow the Gate (for example, files a later slice rewrites); the narrowing ends with that work.

### Releases

How a change in this repo reaches the projects that use the plugin: see [Distribution](architecture.md#distribution).

- **Work on branches.** Every change, a work's documents included, is made on a branch.
- **A merge into `main` is a release.** Projects declare the `flow` marketplace without a `ref`, so whatever is on `main` is what they get. `main` never holds unreleased work: nothing is committed to it directly. Why: [projects declare the marketplace without a ref](decisions-log/2026-10-25-projects-declare-marketplace-without-ref.md).
- **Version and tag.** `/flow:changelog` bumps `.version` and writes the changelog entry on the branch, before the merge. After the merge, tag the merge commit `v` followed by the content of `.version` (`v0.4.0`) and push the tag.
- **Develop with `--plugin-dir`.** Run `claude --plugin-dir /path/to/clone` to load the clone you are editing for one session; it takes the place of an installed `flow` for that session ([flags that load a plugin for one session](https://code.claude.com/docs/en/plugins/cli-reference#flags-that-load-a-plugin-for-one-session)).
- **Never add the marketplace from the clone you are editing.** A marketplace added from a local path loads its relative-path plugins in place, from that folder, not from a copy in the cache ([in-place and copied plugins](https://code.claude.com/docs/en/plugins/loading#in-place-and-copied-plugins)). `flow` has the source `./`, so every session would run the conductor from the files it is editing: the conductor would edit itself. A user also has one marketplace per name ([marketplace reference](https://code.claude.com/docs/en/plugins/marketplace-reference)), so the local one would replace the released `flow`.

## Decisions matching the slice

- docs/decisions-log/2026-11-03-guidelines-are-a-folder-and-sources-goes-away.md — Guidelines are the files of <root>/guidelines/, chosen per slice by description; the sources: key goes away. Rules out a fourth mechanism.
- docs/decisions-log/2026-11-03-guidelines-territory-stops-on-an-old-adapter.md — A guidelines territory whose adapter answers op: list with unmet stops the run; Flow never degrades to the scheda alone.
- docs/decisions-log/2026-10-26-stack-presets-are-replaceable-skills.md — Stack knowledge ships as opinionated flow:stack-<name> skills; a project or user maps a stack to a custom skill that replaces the shipped one entirely.
- docs/decisions-log/2026-10-26-atomic-commit-criteria-absorbed-not-a-dependency.md — One commit per task under atomic-commits' criteria, written once in Flow's executor prompt.

## Project lenses

none (docs/impact-lenses.md absent). Presets: none (stack generic).

## Decisions matching the plan's Files (added at review)

- docs/decisions-log/2026-10-25-projects-declare-marketplace-without-ref.md — Projects declare the flow marketplace without a ref and main holds released states only; rules out per-project tag pins and release channels as the default.
