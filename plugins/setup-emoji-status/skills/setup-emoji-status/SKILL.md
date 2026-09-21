---
name: setup-emoji-status
description: >
  Set up the session-title emoji status convention in the current project — write
  the "Session titles" section into the repo's agent instructions and wire the
  prefixes into the repo's own skills. Use when the user invokes
  /setup-emoji-status or says "set up emoji status", "adopt the session title
  prefixes here", "add the emoji lifecycle to this repo".
---

Install the session-title lifecycle prefixes in the current project: the vocabulary in the repo's
agent instructions, and the prefix changes it promises wired into the skills that own them.

The convention itself is `template.md`, beside this file. Read it before writing anything; it
carries the installed text between its `BEGIN`/`END` markers, and the reasoning for the parts that
are optional.

## The glossary

Every one of these lands in every project. The wording in each row is tailored — the branch `🚀 `
ships to, the gate that makes a branch `📦 `, the resource `💾 ` warns about — but no row is
dropped for a project that cannot reach its stage yet. An unused row is inert: it costs a line and
settles the wording before the workflow that needs it arrives.

| Prefix | Means                                                             | Set by                                                     |
| ------ | ----------------------------------------------------------------- | ---------------------------------------------------------- |
| `🎨 `  | brainstorming or designing with the user                          | the response that opens the design loop, or a `plan` skill |
| `⏳ `  | implementing                                                      | the response                                               |
| `🔍 `  | auditing against live state, with a write to follow               | the response                                               |
| `🔓 `  | about to take the single slot — queued, blocked, or just released | the skill that owns the lock                               |
| `🔒 `  | holding the single slot                                           | the skill that owns the lock                               |
| `💾 `  | writing to the live resource every session shares                 | the response                                               |
| `📦 `  | done on the branch — gated, shippable without re-running          | the gate skill, otherwise the response                     |
| `🚀 `  | shipping to the default branch, or shipped                        | the `ship` skill, as its step 0                            |
| `🚙 `  | parked: the work is sound and waiting on the user                 | the response that hands back                               |
| `⏲️ `   | waiting on a task scheduled for later                             | the response                                               |
| `🪦 `  | dead end — kept for the findings, not to resume                   | the response                                               |
| `📚 `  | extracting learnings into the instruction files                   | the response that invokes `learn`                          |

Precedence, when two could apply: a hazard (`💾 `, `🔍 `, `🔒 `) outranks a park, because the
warning to other sessions comes before the one to the user, who is already reading the response.
`🚙 ` outranks `⏲️ `, because a person can act and a clock cannot. `🎨 ` outranks `🚙 ` for as
long as the design loop is live. `⏳ ` loses to everything.

"Set by" is the difference between a prefix that stays true and one that goes stale: a skill re-reads
its own procedure every run, while a response only sets what the instructions remembered to ask for.
Step 3 is where that half is installed.

## 0. Put `⏳ ` on this session first

`mcp__ccd_session_mgmt__get_session` with `"self"` for the id and title, then `set_session_title`
with `⏳ ` in front of the existing title, replacing any prefix already there. Do it before
reading the repo, not after the files land — a convention whose own installer does not follow it
will not survive its first week. Say nothing about it in the response, then or ever.

`⏳ `, not `📚 `: this is implementation that happens to write instruction files, and
`📚 ` is for a session lifting its own findings into them. The prefix says what the session
is doing, not which paths it edits. From here the session follows the convention it just installed
— `🚀 ` when step 5 lands it, `🚙 ` if it closes on something for the user.

## 1. Find the one home for instructions

- **`AGENTS.md` exists** — it is the home. Add the section there.
- **Only `CLAUDE.md` exists** — add the section to `CLAUDE.md`. Do not split the repo's instructions
  across two files to match a convention it has not adopted.
- **Neither exists** — create `AGENTS.md` with a one-line description of the project above the
  section, and a `CLAUDE.md` holding `@AGENTS.md` and a line saying instructions live there and are
  shared with other agent tools. This is the blank-project path, and it is the whole of it: do not
  scaffold anything else.

A repo whose instructions live somewhere else again — `.cursorrules`, `.github/copilot-instructions.md`
— gets the section in the file the repo actually maintains, not a new one.

## 2. Read the repo before writing the table

The glossary is fixed; what is tailored is the wording inside the rows and which paragraphs go
under the table. Answer these from the repo, not from the template:

- **What ships, and to where.** The default branch name goes in the `🚀 ` row. A repo that ships
  through PRs and a repo that fast-forwards `master` are both fine; the row names the one it does.
- **What the gate is** — the test, lint and build commands that make a branch `📦 `. No gate at all
  is an answer too: `📦 ` then means done on the branch, and says so.
- **What is shared across worktrees.** This is the part that earns the section its place. A live
  database, a real account, a deploy target, a dev-server port, a device, a config file outside the
  repo — anything two sessions can race on. Name it in the `💾 `, `🔍 ` and `🔒 `/`🔓 ` rows and
  write the paragraph `template.md` pairs with each. A repo with nothing shared still keeps the
  rows: the whole glossary ships everywhere, and a row for a stage the repo cannot reach is inert
  until it can — what is skipped is the paragraph, not the line in the table.
- **What the repo waits on the user for** — a credential, an OAuth click, a look at a branch. That
  list is the `🚙 ` row.
- **Which skills the repo already has** (`.claude/skills/`, `.github/skills/`), and what each one
  would set. That is step 3.
- **What a cloud session cannot do here** — credentials outside git, a push convention the cloud
  harness refuses, hardware it does not have. If any stage is out of reach, say which and why, and
  where a cloud session ends instead.

Fit the section to the repo's existing voice and line width, and keep it inside whatever budget the
instructions file is already held to.

## 3. Wire the skills that own a prefix

Edit the skills the repo already has, and write the one missing skill that `🚀 ` cannot do without
(step 3a). A missing `test` skill is not this skill's job: a repo with no gate has no `📦 ` to own,
and the row says so instead.

- **`ship`** — set `🚀 ` as step 0, before the gates run, replacing whatever prefix was there rather
  than stacking. Add the tail too: if the push fails or the ship is abandoned, the title goes back
  to what is true now (`📦 ` for a tested branch, `⏳ ` back to implementing, `🚙 ` waiting on the
  user); if it lands, `🚀 ` stays until another stage replaces it, and is never cleared to leave a
  bare title.
- **The skill that takes a lock**, where there is one — `🔓 ` before the acquire, `🔒 ` once it
  reports acquired, `🔓 ` before the release, then `📦 ` or whatever stage the branch actually
  reached. Each with "do not report this".
- **`learn`** — the repo's `learn` is usually the user-level one, which this skill does not touch.
  So the instructions carry the rule instead: `📚 ` goes on in the response that invokes `learn`,
  before anything is read. Say in the instructions that the skill does not set it itself.

Where a repo's skill already sets a prefix, keep the emoji it uses and make the instructions match
the skill — the skill is the thing that runs.

## 3a. When there is no `ship` skill, write a minimal one

Every project ends up with a `ship` skill; a new one just has not written it yet. So a repo without
one gets it here rather than a `🚀 ` row that waits on a skill nobody has scheduled — it is the one
prefix whose whole value is that nobody has to remember it. Minimal means minimal: the prefix, the
repo's own gate, the commit, and the landing it already does. It is not a port of another repo's
ship skill, and it does not introduce a workflow the repo has not chosen.

Derive the landing shape from the repo, and say in the skill which one it is:

- **Merge commits or a PR in the history, or a remote with branch protection** — commit, push the
  branch, open the PR. The skill ends at the open PR; `🚀 ` stays through it.
- **A linear history on the default branch** — commit, rebase, fast-forward, push, as the sibling
  repos do.
- **No remote at all** — commit on the default branch and stop. Still worth a skill: it owns the
  gate and the prefix.

The skill's step 0 is the `🚀 `, before the gate. Its last step is the restore: `📦 ` if the branch
is tested but did not land, `⏳ ` if the work goes back to implementing, `🚙 ` if it is waiting on
the user — and `🚀 ` left in place, never cleared, if it did land. Both steps carry "do not report
this", and the skill points at the instructions file's session-titles section rather than restating
it.

Where the repo keeps its skills is wherever it already keeps them — `.claude/skills/ship/SKILL.md`
unless a `.github/skills/` tree exists. A blank project gets `.claude/skills/`.

## 4. Revise in place, never append a second copy

A repo that already has a session-titles section gets it corrected, not duplicated: reconcile the
table against what the skills actually set, fill in the rows it is missing, and write the paragraph
for a hazard the repo has grown into. Rows are not dropped — a workflow the repo no longer has
leaves an inert row, not a gap. Same for a repo that carries a thinner version of the vocabulary
under another heading: fold it in and keep the heading it already uses.

## 5. Land it

Follow the repo's own `ship` skill if it has one; otherwise commit. Commit only the instruction
files and the skills this skill edited, and say what was left behind — never sweep the rest of the
working tree in. A project that is not a git repo gets the files and a line saying nothing was
committed.

Report what landed: which file carries the section, which prefixes the repo ended up with, which
skills now set one, and whether a `ship` skill was written. Never report the prefix on this session's own title.
