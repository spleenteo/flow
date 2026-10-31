---
work: doc-territories
slice: V5
updated: 2026-10-31
tags: [work, doc-territories, spike]
description: "Spike for V5: where the session scratch folder lives, `mktemp -d` or the git dir's `flow-scratch/`, tested for a background reader and for main-session writes in default and auto modes"
---

# Spike — where the scratch folder lives

## Questions

1. May a background `reader` subagent (`tools: Read`, `background: true`) `Read` a packet in a `mktemp -d` folder outside the working directories, under default permissions and under the user's `auto` default?
2. The same for `$(git rev-parse --absolute-git-dir)/flow-scratch/`, in a plain checkout (inside the working directory) and in a linked worktree (`.git/worktrees/<name>/`, outside the worktree's working directory).
3. May the main session write there (`Write`, `cp`, `mkdir`) without a prompt?

Rule (V5-plan, "Place rule, for both modes"): `git-dir` wins only if, in D and in A, s2 and s2w are allowed, s4 and s7 are not denied, and s1 is denied in at least one mode. Otherwise `mktemp -d` stays.

## How each was tested

- Claude Code 2.1.316, headless, fixtures in the session scratchpad: `v5-spike` (a repo, `README.md` = `SPIKE-REPO-0`, one commit), `v5-spike-wt` (its linked worktree, branch `wt`), plugin `v5-probe` (agent `reader`: `tools: Read`, `model: haiku`, `background: true`).
- Markers: `SPIKE-TMP-1` in `$X/packet.md` (`X=$(mktemp -d)`, under `/var/folders/…/T/`), `SPIKE-GIT-2` in `$G/packet.md` (`G=<main git dir>/flow-scratch`), `SPIKE-WT-4` in `$GW/packet.md` (`GW=<worktree git dir>/flow-scratch`). The worktree's git dir is `…/v5-spike/.git/worktrees/v5-spike-wt`.
- Command shape, prompt on stdin, no `--add-dir`, no permission flag, `--allowedTools` as the probe needs: `cd <repo> && claude -p --output-format stream-json --verbose [--setting-sources project,local] --plugin-dir …/v5-probe --allowedTools "<list>"`.
- Mode D: with `--setting-sources project,local` (the repo has no settings, so user settings are skipped). Every D run's `init` `.permissionMode` read `default`.
- Mode A: without that flag, so user settings apply (`defaultMode: auto`). Every A run's `init` read `auto`.
- Probes: s1 (reader on `$X/packet.md`), s2 (`$G/packet.md`), s3 (control: `$S/v5-spike/README.md`), s2w (`$GW/packet.md`, run from `v5-spike-wt`); s4 (`Write` `$G/w.md`), s5 (`Write` `$X/w.md`), s6 (`Bash(mkdir:*) Bash(mktemp:*)`: `mkdir -p "$(git rev-parse --absolute-git-dir)/flow-scratch/s6"`, `mktemp -d`), s7 (`Bash(cp:*)`: `cp README.md $G/c.md`).
- Reruns: the first s6 run had its prompt mangled by the runner's shell quoting, so it was rerun (the first output sits in `v5-s6out/`). The runs were launched in parallel, so s5-A found `w.md` already written by s5-D; s5b hit the same race, and s5c (`$X/w3.md`, A only, run alone) is the clean A result.
- Evidence, per run: the marker in the subagent's `tool_result` (messages with `parent_tool_use_id`), the subagent's denial text there (`Permission to use Read has been denied…`), and the main `result`'s `permission_denials` (the same Read, with its path). Files: `$S/v5-spike-<probe>-<D|A>.jsonl`.

## Outcome

Control s3 is allowed in both modes, so s1 and s2 are conclusive in both.

| Probe | Mode D (`default`) | Mode A (`auto`) |
| --- | --- | --- |
| s1 background Read in `mktemp -d` | denied: subagent `tool_result` denial, and `permission_denials` lists the Read of `$X/packet.md`; the reply was `NO RESULT` | allowed: `SPIKE-TMP-1` in the subagent's `tool_result`, no denials |
| s2 background Read in `.git/flow-scratch` (plain checkout) | allowed: `SPIKE-GIT-2`, no denials | allowed: `SPIKE-GIT-2` |
| s2w background Read in the worktree's git dir | denied: subagent denial, `permission_denials` lists `…/.git/worktrees/v5-spike-wt/flow-scratch/packet.md` | allowed: `SPIKE-WT-4` |
| s3 control, Read in the repo | allowed: `SPIKE-REPO-0` | allowed |
| s4 `Write` in `.git/flow-scratch` | denied: "path is inside `.git/`, … sensitive file", in `permission_denials` | allowed (`ok`) |
| s5 `Write` in `mktemp -d` | allowed (`ok`) | allowed (s5c, run alone: `ok`, file written) |
| s6 `mkdir -p "$(git rev-parse --absolute-git-dir)/…"` / `mktemp -d` | `mkdir` denied (a `permission_denials` entry for the Bash `mkdir`; the reason "a `.git` write combined with `git`" is the model's wording, not the tool's); `mktemp -d` allowed | both allowed |
| s7 `cp` into `.git/flow-scratch` | denied: "sensitive location", in `permission_denials` | allowed |

Reading the rule: s1 is denied in D, so that condition holds. But s2w is denied in D, and s4, s7 are denied in D, so `git-dir` fails the rule. In a linked worktree the git dir sits outside the working directory; in a plain checkout `.git` is inside it, which is why s2 passes and s2w does not. Neither place lets a background reader through in default mode under `-p`: `mktemp -d` is denied (s1-D), and the git dir is denied in a worktree (s2w-D). In auto mode both read fine under `-p`, but that is the documented `-p` exception (the first-read prompt "doesn't appear in non-interactive `-p` runs or background sessions"), so A proves nothing about an interactive session. Both modes are expected to prompt in an interactive session: default mode through the sub-agents quote (a background subagent's prompt surfaces in the main session), auto mode through the first-read prompt for a path outside the working directories (permission-modes: "which prompts you"). `-p` hid both: a denial in D, silence in A. Neither was tested interactively here.

## Docs

- Sub-agents, background subagents: "Background subagents surface every permission prompt in your main session. When you answer one of those prompts with a choice that lasts beyond that one tool call, such as a grant that lasts for the rest of the session, Claude Code applies your answer to the whole session, including your main conversation." <https://code.claude.com/docs/en/sub-agents>
- Permissions, working directories: "By default, Claude has access to files in the directory where you launched it." And: "Files in additional directories follow the same permission rules as the original working directory: they become readable without prompts apart from the network path check, and file editing permissions follow the current permission mode." <https://code.claude.com/docs/en/permissions>
- Permission modes, protected paths: "Writes to a small set of paths are never auto-approved, except in `bypassPermissions` mode…"; for `default` they are "Prompted", for `auto` "Routed to the classifier"; protected directories include `.git`. <https://code.claude.com/docs/en/permission-modes>
- Permission modes, first read outside the working directories (auto): "The first time Claude uses the Read, Grep, or Glob tool on a path outside them, Claude Code asks whether to allow that read. The prompt doesn't appear in non-interactive `-p` runs or background sessions; reads there run as before." <https://code.claude.com/docs/en/permission-modes>

The probes agree with the docs: `.git` writes by the main session are refused in default mode (s4, s6, s7), and a read outside the working directories is refused by a background reader in default mode (s1, s2w).

Place: mktemp -d

Decision candidate: Neither place passes for a background reader in default mode: `mktemp -d` is denied (s1-D) and the git dir is denied in a worktree (s2w-D) and for main-session writes (s4-D, s7-D). So V5 keeps the place (`mktemp -d`, outside the repo, one place with no branch on the checkout kind) and the runner as they are. Both modes are expected to prompt in an interactive session (default mode through the sub-agents quote, auto mode through the first-read prompt), and `-p` hid both: a denial in D, silent in A. A read there prompts, and a session-wide grant covers the rest of the session. Proposal outside V5, not carried by any task: tell the user to expect that first prompt.
