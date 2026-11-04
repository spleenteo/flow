# Development guidelines

Rules the plans and the changes in this repo must follow. The repo is a Claude Code plugin made of Markdown skills: there is no build and no test framework.

## References

- The brief behind this layout: [Guidelines as a folder](briefs/2026-11-03-guidelines-folder.md).
- How a change is released: [`docs/guidelines/releases.md`](guidelines/releases.md); how commits are written: [`docs/guidelines/commits.md`](guidelines/commits.md). The other files of `docs/guidelines/` travel with them.
- The skills hold the behaviour: [`skills/`](../skills/), the conductor in [`skills/dev/SKILL.md`](../skills/dev/SKILL.md).

## Tests

Level: none
Sensitive areas:

No test framework. A slice is verified by running what it changed:

- `claude plugin validate .` for anything under `.claude-plugin/` or `skills/`;
- a throwaway test repo, created outside this repo (in the session scratchpad or `/tmp`), for loading, naming and settings behaviour. Never committed;
- for a change to a skill's instructions, a read-through against the brief and the skills that cite it.

Every behaviour of Claude Code a document states is either verified in a spike or a slice, or linked to the official docs page that says it.

## Gate

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
