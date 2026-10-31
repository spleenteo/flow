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
