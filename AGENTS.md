# AGENTS.md

Emotive: a session-title status convention for Claude Code, shipped as the
`emotive-setup` skill in a plugin named `emotive`. The whole product is one
skill — `SKILL.md` and the `template.md` it installs from; everything else is
packaging.

## Layout

| path | what |
|---|---|
| `plugins/emotive/skills/emotive-setup/SKILL.md` | the install procedure — what the skill does when it runs |
| `plugins/emotive/skills/emotive-setup/template.md` | the convention itself: the text that lands in a project, between its `BEGIN`/`END` markers |
| `plugins/emotive/.claude-plugin/plugin.json` | version; gates `claude plugin update` |
| `.claude-plugin/marketplace.json` | the marketplace listing that serves the plugin |
| `build.sh` | syncs the glossary table from `template.md` into the README |
| `.github/workflows/tag-release.yml` | tags `v<version>` when a bump lands on `main` |

## Editing the skill

1. Edit `SKILL.md` (how the install runs) or `template.md` (what it installs).
   The two have different jobs: a rule an installing session follows goes in
   `SKILL.md`, a rule the *installed* project follows goes in `template.md`.
2. Run `./build.sh` — the README embeds the glossary table between
   `glossary:begin` / `glossary:end` markers. Never hand-edit that block; it
   will be overwritten. The build fails on a placeholder it has no generic
   wording for, so a new `<placeholder>` in the table needs a line in its
   `GENERIC` map.
3. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json`
   for anything that should ship. Without a bump, `claude plugin update` reports
   "already at the latest version" even when `main` has new commits. `/ship`
   does this. An install delivers `plugins/emotive/` and nothing else, so a
   change entirely outside it — this file, the README, the repo's own skills —
   lands without a bump and without a tag: bumping would announce an update that
   changes nothing on an installed machine. That is about the plugin channel
   alone. The README is the package's front page and is public the moment it
   lands on `main`, which is where someone deciding whether to install reads it.

The local install is a symlink: `~/.claude/skills/emotive-setup` points at
`plugins/emotive/skills/emotive-setup`, so an edit here is live in the next
session with no build or install step. Installing the plugin on this machine too
would list the skill twice.

Landing that bump on `main` tags the release from CI. Never tag by hand: a cloud
session cannot push `refs/tags/*` at all, so a tag step in the local workflow is
one more thing that silently only works from a laptop.

An emoji is two columns wide, and a variation selector is zero but widens the
character before it, so a markdown table padded by character count comes out
ragged. `build.sh` pads the generated glossary by display width; a table written
by hand here needs the same arithmetic, or prettier's.

The README shows a prefix as a bare emoji — 📚, never `📚 `. The code formatting
and the trailing space belong to `template.md`, where a session reading it is
about to set a title; `build.sh` strips them from the generated table, and prose
in the README follows the same rule.

## The vocabulary is shared, not invented here

The glossary came from the sibling repos that use it — `karabiner` and `axshot`
first, then `github-triage`, `email-filter-builder`, `a-thousand-worlds`,
`regard` and `blunt` — and their `AGENTS.md` files are the field copies. A
change to the meaning of a prefix is a change to all of them: make it here, then
carry it across, or the sidebar stops reading the same way from one repo to the
next. `~/projects/karabiner/docs/workflow.md` holds the original reasoning.

Adding a row costs a line in every project and nothing else, since an unused row
is inert by design. Changing or removing one costs a sweep.

Say a prefix is **set in the response that enters the stage** — never "by hand",
which reads as something the user does when every setter is an agent. The
distinction the wording has to carry is whether a skill owns the stage and
re-reads it each run, or a response sets it inline from an instruction it has to
remember.

## Evaluating a change

Do not reason about what an instruction "would" cause and call that a result.
Run the skill against a real repo — a scratch `git init` for the blank-project
path, a clone of a sibling repo for the revise-in-place path — and read what it
wrote. A section that reads well here and lands badly there is the failure this
repo exists to catch.

Check both halves: the section in the instructions file, and the skills the
install claims to have wired. A `🚀 ` row with no owner is the bug that keeps
coming back.

## Testing your own output

This skill's convention governs the session that runs it too. A session working
in this repo carries a prefix like any other, and never mentions it.

## Reporting

Never suggest restarting Claude Code. After `/ship`, end on the last bullet — no
"Done.", no "Ready to …" line; nothing is waiting on the user.

## Git

Conventional-commit subjects (`docs:`, `feat:`, `fix:`), matching the history.
Work happens on a branch in a worktree and lands on `main`; `/ship` is that
procedure.

## Session titles

A lifecycle prefix on the session title says what a session is doing while it is
doing it, so the sidebar answers "which of these is mid-ship" without opening any
of them. The sidebar already shows a status dot (running / awaiting input / idle)
and a branch glyph for worktree sessions; neither can be set from here —
`set_session_title` takes a title string and nothing else. So a **single leading
emoji on the title** is the only lever, and it is spent on what the app cannot
know: where the work stands.

| Prefix | Means |
|---|---|
| `🎨 ` | brainstorming or designing with the user — exploring, sketching, deciding what to build |
| `⏳ ` | implementing — the weakest of them; every other prefix takes precedence |
| `🔍 ` | auditing against live state — a dry run, or the plan it printed, with a write to follow |
| `🔓 ` | about to take that slot — queued or blocked on it — or just released it |
| `🔒 ` | holding a single slot only one session can use at a time |
| `💾 ` | writing to a live resource every session shares |
| `📦 ` | done on the branch — `./build.sh` clean and shippable without re-running anything |
| `🚀 ` | shipping to `main`, or shipped |
| `🚙 ` | parked: the work is sound and waiting on the user (a decision, a review) |
| `⏲️ ` | waiting on a task scheduled for later — nothing to do until it fires |
| `🪦 ` | dead end — kept for the findings, not to resume |
| `📚 ` | extracting learnings into `AGENTS.md`, the README or the skills |

`🔍 `, `🔒 `, `🔓 ` and `💾 ` are inert here — nothing in this repo is shared
across sessions. They are listed because the glossary ships whole, which is the
rule this repo teaches; a repo that made an exception of itself would be arguing
against its own product.

**Handing back is itself a stage.** A response that closes on something for the
user to do — a decision, a review, a look at the branch — is a park, and `🚙 `
goes on before that response, since the idle dot cannot tell "waiting on you"
from "given up on".

**A design loop is not a park.** `🎨 ` holds through brainstorming and outranks
`🚙 ` while it does: the back-and-forth _is_ the stage, so a park prefix on every
turn of it marks the session as blocked without saying on what. It becomes `🚙 `
once the design is settled and waiting on a decision, and `⏳ ` when that
decision comes. No skill here sets it; it goes on in the response that opens the
loop.

**Never mention a prefix in the response** — not what it was set to, not that it
was already right, not that it was left alone. It is sidebar state; say nothing
about it unless asked.

These are **stages, not flags**: exactly one prefix at a time, and setting a new
one replaces whatever was there — only one reads cleanly at sidebar width, and
`🚀 ` after `📦 ` is noise, since the later stage implies the earlier. **Every
title carries one**, and a prefix comes off only when another takes its place: a
bare title says nothing about the session, and the sidebar cannot tell it apart
from a chat that never had a stage at all. A session with nothing left to do
keeps the prefix of the last stage it reached. The harness names a session, so
every session starts without a prefix: putting the first one on that inherited
title is part of the first response, not something to wait for a stage change to
prompt.

Set a prefix **optimistically** — when the stage _starts_, not when it succeeds —
and correct it if the stage falls over. A title that only becomes true at the end
is blank for the whole stretch the sidebar is there to describe. `🚀 ` is set by
`/ship` as its first step, before `./build.sh`, and `/ship` puts back what is
true instead if the ship does not land — `📦 ` for a branch that built clean,
`⏳ ` if the work goes back to implementing, `🚙 ` if it is waiting on the user —
so it stays true on its own. `📚 ` goes on in the response that starts
extracting the session's learnings, before anything is read: whatever carries
out the extraction will not set a title itself. The rest are set in the response
that enters the stage (`mcp__ccd_session_mgmt__set_session_title`), and nothing
reconciles a title against reality: an abandoned session keeps whatever prefix
it had.

**Ask which session this is before renaming one.** `get_session "self"` is the
only answer, and it changes under a fork: a forked session carries the whole
transcript, the id it read earlier in it, and a different id of its own, so a
rename that reuses the remembered one retitles the session it forked _from_. A
fork also starts in the worktree of the session it forked from, and nothing stops
a branch being checked out there, which moves that worktree under the other
session's feet; put it back on the branch it was on once the work has landed.
