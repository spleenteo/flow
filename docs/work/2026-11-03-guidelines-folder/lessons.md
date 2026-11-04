# Lessons — guidelines-folder

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
