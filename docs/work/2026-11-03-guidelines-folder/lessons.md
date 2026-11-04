# Lessons — guidelines-folder

One rule per line, read by every later subagent.

## From V1

- When a packet starts carrying a whole file, run the link check on the packet too: relative links copied verbatim break from docs/work/<slug>/.
- An instruction that names a moment twice ("once, at impact and at the packet") is two rules: write each moment as its own clause.
- When a plan rewrites how one call's args are built, change the paragraph that defines the call and point to it from elsewhere; never restate the args in a second place.
- An executor whose Done says "agrees with § X" reads § X before reporting; a task-local wording check is not that check.
- On macOS, never edit with sed -i in an executor: it fails silently on BSD sed; use the Edit tool and re-read the changed line.
