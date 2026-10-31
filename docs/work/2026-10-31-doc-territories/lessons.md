# Lessons — doc-territories

Rules learned while executing, one per line. Every plan and packet carries them.

- In `claude -p`, `--allowedTools "<list>"` is variadic and swallows a prompt argument after it; pipe the prompt on stdin.
- A Verified line that forbids a literal (`rg -n '\*-<slug>'` → no output) also forbids quoting it in prose; plan the check to match only the command, or word the prose without it.
- A slug lookup by substring (`rg "<slug>"`, `*-<slug>`) matches longer slugs; anchor it on the exact field (`^work: <slug>$`, or the backticked slug with `-F`).
- A skill's glob over `<root>/work/*/` gets `2>/dev/null`, as `shared/work.md` does, so an empty folder reads as no match.
