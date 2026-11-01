# Territories

Flow ships no adapter. A project that keeps a kind of document somewhere other than the repo declares it in `.flow.yml`, and an adapter skill from another plugin reads, writes and searches there. The adapter runs in the conductor's own session, never in a subagent and never behind `flow:docs` ([decision](../docs/decisions-log/2026-10-31-territory-adapters-run-in-the-conductor.md)). `flow:docs` stays on repo files.

This file is the whole contract: someone who has read only it can write an adapter, and a conductor can call one.

## Declaring

`territories:` in `.flow.yml` holds one entry per kind, block style, each value a flow map:

```yaml
territories:
  decisions: {adapter: acme-wiki:territory, where: ENG/decisions, write: comment}
  product: {adapter: acme-wiki:territory, where: PROD/context, write: direct}
```

The kinds, and where an undeclared kind lives (the repo, under `<root>/`, as without `territories:`):

| Kind | Undeclared, it is |
|---|---|
| `decisions` | `<root>/decisions-log/` |
| `changelog` | the `changelog` path |
| `guidelines` | `<root>/development-guidelines.md` |
| `lenses` | `<root>/impact-lenses.md` |
| `product` | nothing: no product document is read |
| `archive` | `<root>/work/archive/`, with `archived.md` |

`work/` and `.version` are never territories. Any other kind is a malformed entry.

Each entry has three fields:

- `adapter` (required): the adapter skill's name as the session lists it, `<plugin>:<skill>` for a plugin's skill.
- `where` (required): the place, in the adapter's own terms (a path, a space and collection, a project key). Flow never interprets it, and quotes it verbatim in repo files (the text that replaces an archived work's inbound links), so it never carries a token or a share id ([Tokens, per-user values and links](#tokens-per-user-values-and-links)).
- `write` (optional): a word the adapter's `write` operation defines. Absent, the adapter's default applies.

**Changelog.** A `changelog` territory takes the place of the `changelog` path. `labels`, `language` and `decisions` still apply.

**No presets.** Territories never go through `presets:` or `stack_presets`. `presets:` maps a stack to a skill; a territory is a place for documents, and the two keys stay apart so a territory is never read as a stack.

Without `territories:`, nothing is read, resolved or shown, and every kind stays in the repo.

## Resolution

The conductor resolves territories once, when it opens or resumes a work: after the upgrade check, before it reads the profile (the profile may read a `guidelines` territory).

- It reads `.flow.yml` and takes the adapter name of each entry, then invokes the adapter with the Skill tool, by that name, never by path.
- **One invocation per adapter per step.** The args hold the step's request lines for every territory that adapter serves: one request per line, separated by newlines, and the adapter answers in the same order. Territories served by different adapters get one invocation each.
- **Stops.** The run stops with one message that names the territory, and no work is opened or resumed, on: a malformed entry (unknown kind, or no `adapter` or `where`); an adapter the session doesn't list (exact name); an unmet `Requires`; any operation that returns `unreachable`. It never falls back to the repo. The exception is an `op: write` inside a step, which follows § Writes › Returns.

**Request line.** Several per territory are allowed, one per line of the args. Fields are separated by `; `, list values by `, ` (not by bare commas, since keywords may hold commas):

```text
op: search; kind: decisions; where: ENG/decisions; write: comment; keywords: cart, total; files: app/models/cart.rb
```

Every request carries `op`, `kind` and `where`; `write` is carried when declared; the other fields depend on the operation (see [Operations](#operations)).

**Returns.** For each request, in order, the adapter returns a block headed `<kind>:`, followed by the operation's return. `search` and `list` return at most ten lines. A longer result goes to a scratch file instead, and the adapter returns one line, `<kind>: results: <absolute path>`.

**Requires before every operation.** The adapter checks its `Requires` before any operation. If one is unmet, it returns `unmet: <what>` and does nothing else.

**Opening line.** When every adapter resolves, the conductor's opening message carries one line per declared territory: `<kind> → <where> (<adapter>, write: <write | default>)`.

**`op: none`.** The request that runs `Requires` alone, with nothing read or written. It returns `ready` or `unmet: <what>`. In a step with reads, it is the line for each declared kind that has none, in the same invocation (§ Reads).

## Reads

What the conductor fetches through an adapter for a declared kind, and how it hands the result on. An undeclared kind reads as without `territories:`.

**By moment.** Each row is one request line per kind.

| Moment | Kind | Request |
|---|---|---|
| Opening or resume, before the profile | `guidelines` | `op: read` |
| Opening, Step 1's memory search | `decisions`, `changelog`, `archive` | `op: search`, with the request's keywords |
| Opening, Step 1's slug check | `archive` | `op: list`, matched on `work` anchored (§ Operations) |
| `/flow:archive` step 2 | `changelog`, `decisions` | `changelog`: `op: read`; `decisions`: `op: list` |
| Resume (standing documents) | `decisions` | descriptions, keywords from `STATUS.md`'s `description:` |
| Impact; slice packet; resume at `step: plan` | `decisions` | descriptions with the slice's files |
| Impact; slice packet; resume at `step: plan` | `lenses` | `op: read` |
| Impact; slice packet; resume at `step: plan` | a matched source naming a territory | `op: read` with `name` |
| After the plan, before the review dispatch | `decisions` | descriptions again, with the planner's `Files:` |
| After the plan, before the review dispatch | a newly matched source naming a territory | `op: read` with `name` |
| Opening of `framing-doc` and `shaping`; the conductor writing `frame.md` itself | `product` | `op: read` |
| Close, changelog step | `changelog` | `op: read` |
| Close, changelog step | `decisions` | `op: list`, for the ids (§ Writes › Changelog) |

**One invocation per adapter per step.** Every declared kind the adapter serves gets a line in the same invocation: its read or, when the step has no read for that kind, `op: none`. The conductor copies each `read` and `results:` file it keeps into the scratch folder (each `read` as `<kind>/<name>.md`, `<kind>/document.md` for a single document); reuse and `excerpts:` point only there. A result already in the scratch folder this session is reused (the `list` result within a slice; the `product` copy for every block of the session; a resume in a fresh session has none, so it reads the guidelines again).

**Descriptions** are an `op: search` (keywords) plus an `op: list` (no filter) in one invocation. The conductor does the `touches` match on the `list` results file with `rg` or `awk`, by `flow:docs` § Op `search` › `files`'s rule; a block of ten lines or fewer is matched as returned. It reads only the matched lines. It keeps the search hits and the matches, one line each:

```text
<kind>:<identifier> — <description>
```

**Excerpts.** The conductor writes those lines to a file in the scratch folder and passes its absolute path to `flow:docs` as the `excerpts:` header key of `search`, `descriptions` and the changelog ops. For `search` and `descriptions` the file holds the lines above; for the changelog ops, the copy of the file the changelog `read` returned. `flow:docs` takes the lines as matches, merges them with the repo's, keeps ten, and leaves its output format unchanged. The changelog ops take tone and language from the excerpts instead of `<changelog>`; `changelog-write` writes the entry to `<scratch>/CHANGELOG.md`, never `<changelog>` (§ Writes › Changelog). With a `changelog` territory declared, `search` also carries `repo-changelog: skip`, so a repo changelog left behind is not searched.

**Scratch folder.** One `mktemp -d` per session, made at the first territory read or write and reused (the `/flow:design` § Check pattern). It is outside the repo and never committed.

**Guidelines on a territory.** `## Tests` and `## Gate` come from its `read`: no branch lookup, no local file. A missing `## Tests` is asked, and the asked level is written through the adapter (§ Writes); while `proposed`, it holds for this session only. The gate at close comes from the packet. Neither the draft nor Step 0 offers a guidelines or lenses file for a declared kind. A `read` that returns `missing` means there are no guidelines yet: the profile asks the level, and the `## Tests` write sends a new file holding only `## Tests` (§ Writes). A `lenses` `read` that returns `missing` reads as an absent `impact-lenses.md`: project lenses: none.

**Sources naming a territory.** A `sources:` entry may carry `{territory: <kind>, name: <name>, when: …}` in place of `path`:

```yaml
sources: [{territory: product, name: voice, when: ["app/**"]}]
```

An undeclared kind is skipped and named. There is no `git ls-files` check. Its `when` holds globs only: a `{step: …}` or `{phase: …}` value is skipped and named, since no row above reads a territory source for a step or a phase. The packet heading is `### <kind>:<name>`, with the text of its copy verbatim.

**Product.** When `product` is declared, the conductor reads it through one adapter invocation, `op: read; kind: product` with `op: none` lines for the other declared kinds that adapter serves. It copies the result to the scratch folder and never reads the territory's files directly. For `framing-doc` and `shaping` only, it builds a block named `` `## Product context` `` just before the invocation, from the copy's text verbatim, inside the prompt the tool receives, right below the criterion and above the path instruction. On the definition path the frame phase opens with `flow:brainstorm`, which gets no block; a conductor writing `frame.md` itself reads the product context and never copies it in. The block's first line, right under its heading, word for word: ``Context only: cite it as `product:<name>`, never copy its text into the document you write.`` (`<name>` is the read's name, `document` when none). Undeclared or `missing`: nothing.

**Packet place.** When `territories:` declares `decisions`, `guidelines` or `lenses`, or a matched source names a territory, `V<n>-packet.md` goes in the scratch folder, else in `<work dir>/` as without territories. The ledger's head gets a third line, `Packet: <path>`. Close adds the packet to the work's commit only from the work dir. A resume at execute whose scratch packet is gone rebuilds it in this session's scratch folder, and a `Ruling:` line records the new path.

## Writes

What the conductor sends through an adapter for a declared kind, and what comes back. An undeclared kind is written as without `territories:`: the repo, committed as today.

| Moment | Kind | Requests |
|---|---|---|
| Decision recorded (a conductor, `/flow:grill`) | `decisions` | `op: write` with `name: new` |
| Status changed | `decisions` | `op: read` with `name`, then `op: write` with that `name` |
| Close, changelog step | `changelog` | `op: write` with no `name`, when `changelog` is declared |
| Profile writes the `## Tests` lines | `guidelines` | `op: write` with no `name` |
| Archive (`/flow:archive`) | `archive` | `op: archive` with `folder` |

Every request line carries `op`, `kind` and `where` (§ Resolution); each `op: write` adds `file: <absolute path>` and `op: archive` adds `folder:`; both add `write` when declared. One invocation per adapter per step still holds: the other kinds that adapter serves get `op: none`.

**Scratch folders.** Every writer makes the session scratch folder at its first step that touches a declared territory, if this session has none: the conductor, `/flow:grill`, `/flow:archive` and a manual `/flow:changelog`. Each write gets a fresh, empty folder, `mktemp -d "<session scratch folder>/w.XXXXXX"`, so `decisions-check` sees one file. The request to `flow:docs` carries it as `scratch: <that folder>`.

**Decision.** With `decisions` declared, the request is the work reference's § Decisions request (`shared/work.md`) plus `scratch:`. `flow:docs` writes `<scratch>/YYYY-MM-DD-<slug>.md`, runs `decisions-check` on that folder, commits nothing, and returns `Composed <absolute path> — check: <summary line>`, with no sha. The adapter then gets `op: write; kind: decisions; where: <where>[; write: <word>]; name: new; file: <path>`. On `NEEDS:` or `FAILED:`, nothing is handed to the adapter.

**Status change.** The conductor first runs `op: read; kind: decisions; where: <where>; name: <id>`, then sends `decision-status` with `scratch:` and `file: <session scratch folder>/decisions/<id>.md`. `flow:docs` copies the file to `<scratch>/<its **Date**>-<name less a leading date>.md`, edits it and checks it. Any error removes the copy and returns `FAILED: check: <first error>`. Success returns `Composed <absolute path> — <status> — check: <summary line>`, and the adapter gets `op: write; kind: decisions; where: <where>[; write: <word>]; name: <id>; file: <path>`. `superseded_by` takes the new decision's identifier.

**Changelog.** At the close, whenever `decisions` is declared, the invocation also sends `op: list; kind: decisions; where: <where>` (in the same invocation when `decisions` is on the same adapter, else in its own), so the **Decision** line is kept even when only `decisions` is a territory. `op: write; kind: changelog` is sent only when `changelog` is declared. The conductor runs the anchored match (§ Operations) on the block, which gives `decision-ids: decisions:<id>, …`. `changelog-write`, with `scratch:`, inserts the entry into a copy of `excerpts` (or under a new two-line intro) at `<scratch>/CHANGELOG.md`, writes the version file and commits nothing. It returns `Recorded v<version> — <title> — not committed; changelog: <absolute path>; missing decisions: …; manual after merge: …`. Then `op: write; kind: changelog; where: <where>[; write: <word>]; file: <path>`, with no `name`. On `written` or `proposed`, the conductor commits the version file alone (`Bump the version to vX.Y.Z`); on `unmet`, `unreachable` or `FAILED:`, it restores the version file when git tracks it, else removes it, and records nothing (the work reference, § Closing a work step 2). A territory decision is named `` `decisions:<id>` ``, never linked, since a `file://` link in a repo changelog fails the Gate. With `decision-ids`, the **Decision** bullet stays even without `decisions-log/`. On a changelog territory, a repo decision is named `` `<root>/decisions-log/<file>` ``.

**`## Tests` on a territory.** At `/flow:dev`'s § Profile › Writing (`skills/dev/SKILL.md`), the two lines go into the conductor's copy of the guidelines `read`: at the start of `## Tests`, or in a new section. On `missing`, they go into a new file `<session scratch folder>/guidelines/document.md` holding only `## Tests` with the two lines. Then `op: write; kind: guidelines; where: <where>[; write: <word>]; file: <copy>`, with no `name`. While `proposed`, and on `unmet`, `unreachable` or `FAILED:`, the level holds for this session only.

**Archive.** With `archive` declared, `/flow:archive` sends `op: archive; kind: archive; where: <where>[; write: <word>]; folder: <absolute path of <root>/work/<dir>>`, with `op: none` lines for the adapter's other declared kinds. The adapter keeps the folder as one container named `<dir>`, one document per file, each file as it is (frontmatter included, no fields table); subfolders become nested containers. It returns `archived <name>` (`<name>` is the container's identifier), `proposed <link>` or `FAILED: <reason>`; on `unmet`, `unreachable` or `FAILED:` the archive stops, removing nothing. Re-running on an already archived `<dir>`, with a word that writes in place or when the container already matches the folder (same files, same contents), replaces the territory copy with the folder as it is now and returns `archived <name>`, and with a proposing word when they differ, opens a proposal and leaves the container as it is; re-running on an open proposal replaces the proposal's content and returns the same `proposed <link>`, never a second one. On `proposed`, nothing is removed or rewritten, and `- YYYY-MM-DD — archive:<dir> — <link>` goes under the work's `## Pending proposals` as below; the commit message is `Note a pending proposal on archive`. Whenever `territories:` is declared (any kind), `/flow:archive` reads this contract and makes the session scratch folder first; its step 2 sends one invocation per adapter (§ Reads), with `op: none` for every other declared kind, `archive` included, so `Requires` is checked before anything is touched, and it stops as § Resolution says, with nothing archived. The git steps (the clean check, the censuses, the removal, the text that replaces inbound links) are the work reference's § Archive, repo, on a territory (`shared/work.md`).

**Returns.** `written <id>` is reported as `<kind>:<id>`, a plain `written` (single document) as `<kind>:document`. `proposed <link>` adds the line `- YYYY-MM-DD — <kind>:<name> — <link>` under `## Pending proposals` in `STATUS.md`, a section before `## Log`, created when missing. `<name>` is the id, the composed file's name without `.md` for `new`, or `document` for a single document. Outside a work, the link is only reported. On `FAILED: <id> exists`, the conductor stops that write, reports the id and the composed path, and adds nothing to `STATUS.md`; it does not retry. On an `op: write`, `unmet: …`, `unreachable: …` or any other `FAILED: <reason>` records nothing: the conductor reports the adapter's line and the composed file's absolute path, adds nothing to `STATUS.md` and goes on with its step; the user may ask to resend.

**The pending line.** Before the append, the conductor checks that `git -C <repo root> ls-files --error-unmatch <work dir>/STATUS.md` succeeds and that `git -C <repo root> diff --quiet HEAD -- <work dir>/STATUS.md` exits 0. If both hold, the line is committed alone: `git -C <repo root> add <work dir>/STATUS.md`, then `git -C <repo root> commit -m "Note a pending proposal on <kind>" -- <work dir>/STATUS.md`. Otherwise the line is kept but not committed, and the message says so: a `STATUS.md` that is untracked or missing gets its section when Step 2 writes it, so the line goes with the home commit; a modified one takes it with the next `STATUS.md` commit. The work close's report (`/flow:dev` Step 6 move 5; the work reference's § Closing a work step 5) adds, only when `territories:` is declared, `Pending proposals (as recorded, not re-checked): <kind>:<name> <link>, … | none`.

## The adapter skill

An adapter is an ordinary skill in a plugin. Its frontmatter holds `name` and `description`; `user-invocable: false` is allowed. Two keys are forbidden ([skills reference](https://code.claude.com/docs/en/skills)):

- never `context: fork`: the skill would run in a subagent, which brings back the fork-to-connector path the decision rules out;
- never `disable-model-invocation: true`: the Skill tool could not invoke it.

The body has three sections, in this order. They are `##` headings in the adapter's own SKILL.md (`###` here only because they sit inside this file's § The adapter skill).

### Requires

What the adapter needs to work: a connector or tool it must be able to call, a credential in its own user configuration (see [Tokens, per-user values and links](#tokens-per-user-values-and-links)), a `where` that exists (a missing or wrong `where` is `unmet`, since `Requires` checks it). Say how each is checked per `where`, and what the adapter says when one is unmet: `unmet: <what is missing and how to fix it>`, naming the item.

### Fields

A document's fields are the ones the repo keeps in YAML frontmatter: `kind`, `status`, `description`, `touches`, `work`, `area`, `superseded_by` and `resolved_by`.

- If the service has native fields, map them to these names.
- Otherwise, keep a key/value table at the top of the document, with list values comma-separated and `null` written out:

```text
| Field | Value |
|---|---|
| kind | decision |
| status | active |
| touches | app/models/cart.rb, app/views/cart |
| superseded_by | null |
```

`read` returns the repo's frontmatter form: `description` quoted, lists as `[a, b]`, `null` written out. It writes `<name>.md` into a `mktemp -d` folder of its own (`document.md` for a single document), the frontmatter followed by one blank line. A `write` file with no frontmatter (the changelog, guidelines) is stored as it is, with no fields table. A ` | ` inside a table value is written `\|` and read back as ` | `.

The decision keys (`kind`, `status`, `description`, `touches`, `superseded_by`) must round-trip through `read` and `write` unchanged.

- On kind `archive`, the container's fields are those of its `STATUS.md` frontmatter (`status`, `work`, `description`, …), read from the archived copy; the files inside keep their own frontmatter untouched.

### Operations

Every operation runs `Requires` first and may return `unmet: <what>`, or `unreachable: <reason>` when the service doesn't answer.

| Operation | Input | Returns |
|---|---|---|
| `op: none` | none | `ready` or `unmet: <what>` |
| `op: read` | `name` (optional; absent, the document at `where`, for the single-document kinds `guidelines`, `lenses`, `product`, `changelog`) | `read <scratch path>`: the text, its fields as frontmatter; or `missing` |
| `op: write` | `name: <id \| new>` (optional; absent, it replaces the single document at `where`, or creates it when `read` returns `missing`); `file: <absolute path>`; the fields as frontmatter in that file | `written` (existing name) or `written <id>` (`new`; the id is what later `read` and `list` calls use), or `proposed <link>`, or `FAILED: <id> exists` |
| `op: search` | `keywords`; `files` (optional) | lines `<identifier> — <excerpt>`, or `none` |
| `op: list` | `keywords` (optional); `files` (optional) | lines `<identifier> — <field>: <value>; <field>: <value>`, or `none` |
| `op: archive` | `folder: <absolute path of the work's folder>`; the container is named `<dir>`, the folder's name | `archived <name>` (`<name>`: the container's identifier; with a word that writes in place, or when the container already matches the folder (same files, same contents), a re-run replaces the copy and returns it again), `proposed <link>`, or `FAILED: <reason>` with nothing kept on the territory |

- A request is checked in order: the `write` word, then the identifier. An unknown `write` word returns `unmet: write <word>`; it is checked with `Requires`, so a wrong word in `.flow.yml` already returns it at resolution, `op: none` included. `write` creates documents inside an existing `where`; a missing `where` is `unmet`.
- `name: new`: the new document's identifier is the composed file's name without `.md`, returned as `written <id>`; an existing document with that identifier returns `FAILED: <id> exists`, and with `comment`, so does an open proposal with it.
- `list` lines carry at least `status`, `work` and `touches`, with `description` last, so a `;` inside it cannot break the fields before it. On kind `archive`, `list` lines carry `status`, `work` and `description` only, `description` last.
- A proposal stays out of `list` and `search` until accepted, archive proposals included.
- `op: archive` rules: `<dir>` is the last path component of `folder` (no trailing slash is sent). It needs an existing `where` (else `unmet`) and takes the adapter's `write` words as `write` does. On any other kind it returns `unmet: op archive on <kind>`. The container holds exactly the folder's files, every file kept, dotfiles included (the conductor guarantees the folder is clean, § Archive, repo, on a territory, so the adapter filters nothing); a re-run removes from the container the files removed from the folder since. When the container already holds exactly the folder's files, with the same contents, `op: archive` returns `archived <name>` whatever the word (the case after a proposal is accepted); a proposing word opens a proposal only when they differ, and until it is accepted the container stays as it is, an open proposal being replaced by the next run and returning the same `proposed <link>`; where the proposal lives is the adapter's choice, and the link points at it. The conductor stops on `unmet`, `unreachable` or `FAILED:`, removing nothing.
- On kind `archive`, `list` and `search` give one line per container. `list`: `<dir> — status: <s>; work: <w>; description: <d>`, the values from its `STATUS.md` frontmatter; a container with no `STATUS.md` is left out of `list` and still searched. `search`: `<dir> — <excerpt>`, the excerpt from any file of the container; the conductor passes each hit on as `archive:<dir> — <excerpt>`. No Flow step sends `read` on kind `archive`.
- `proposed <link>`: the link is absolute (a URL, or `file://` with an absolute path) and points at the proposal. Until it is accepted, `read` and `list` return the document as it was (a new one is absent). A `read` of a name that exists only as a proposal returns `missing`.
- `written` means the document is in place. `proposed <link>` means the adapter left a proposal for a person to accept (a comment, a draft) and the link leads to it; the document is not yet changed.
- `search` and `list` return at most ten lines, or write the whole result to a scratch file and return its absolute path. `files` is an optional filter the adapter may apply; the conductor does the `touches` match itself on the fields `list` returns.
- `list` lines use the § Fields names, list values comma-separated. An identifier holds no ` — `. **The anchored match** finds the lines of a returned block whose `work` is a given slug. The conductor puts the block in a scratch file (on `<kind>: results: <absolute path>`, it uses that file) and runs `awk -v s='<slug>' '{i=index($0," — "); if(!i) next; n=split(substr($0,i+length(" — ")),f,"; "); for(k=1;k<=n;k++){if(f[k] ~ /^description: /) break; if(f[k]=="work: " s){print substr($0,1,i-1); break}}}' <file>`, which prints each matching line's identifier whole, comparing `work` exactly and never inside `description`.
- Scratch files come from `mktemp -d`, outside the repo, and are never committed (see [Reads](#reads)).
- The adapter defines its `write` words (for example `direct`, which writes in place, and `comment`, which proposes) and which one is the default when `write` is absent. It documents them in the body of its skill.

## The repo

The reference behaviour: what an undeclared kind does, and what every adapter stands in for.

- **Requires**: none.
- **Fields**: YAML frontmatter.
- **`read`**: the file itself.
- **`write`**: writes the file and returns `written`. The caller commits, as today.
- **`search` and `list`**: `flow:docs` `op: search` and `op: descriptions`.
- **`archive`**: the move `/flow:archive` makes.

## Tokens, per-user values and links

- Tokens and per-user values live in the adapter plugin's own [user configuration](https://code.claude.com/docs/en/plugins/manifest-reference#user-configuration), never in `.flow.yml` or in any file of the repo.
- `where` values, returned identifiers and returned links are identifiers or login-only links, never public share links, and never carry a token or a share id: Flow quotes them verbatim in repo files (`archived on <where>: <name>`, the `## Pending proposals` lines). A local path counts as an identifier: a `file://` link to a local file is not a share link.
- Flow doesn't enforce this. Keeping to it is the user's responsibility.
