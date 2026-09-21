# AGENTS.md

Emotive: a session-title status convention for Claude Code, shipped as a plugin
named `emotive`. The product is in two halves. The **glossary** —
`context/session-titles.md` — is injected into every session by a `SessionStart`
hook, so it needs no install and is identical everywhere. The **wiring** — the
`emotive-setup` skill, with `emotive-setup-interactive` as a front door onto the
same procedure — installs only what a hook cannot know: what a row means in one
particular repo, and the prefix changes its own skills have to set. Everything
else is packaging.

The split is the whole design. A rule that reads the same in every repo goes in
the injected half, where one edit reaches every session. A rule made of a repo's
own commands goes in the skill's half, where it is written into that repo once.

## Layout

| path | what |
|---|---|
| `plugins/emotive/context/session-titles.md` | the glossary — the actual product, injected into every session |
| `plugins/emotive/hooks/hooks.json` | `SessionStart` hook that cats it into context |
| `plugins/emotive/skills/emotive-setup/SKILL.md` | the wiring procedure — what the skill does when it runs |
| `plugins/emotive/skills/emotive-setup-interactive/SKILL.md` | the same wiring, with the local half asked rather than inferred |
| `plugins/emotive/skills/emotive-setup/template.md` | the shape of the local half: what lands in one repo's instructions |
| `plugins/emotive/.claude-plugin/plugin.json` | version; gates `claude plugin update` |
| `.claude-plugin/marketplace.json` | the marketplace listing that serves the plugin |
| `build.sh` | normalizes the glossary table and copies it into the README |
| `.github/workflows/tag-release.yml` | tags `v<version>` when a bump lands on `main` |

## Editing the convention

1. Pick the half. `context/session-titles.md` holds what every session reads —
   the rows, and every rule that is true regardless of repo. `SKILL.md` holds
   what an installing session does. `template.md` holds the shape of what lands
   in one repo's instructions. A rule in the wrong half either reaches nobody or
   reaches everybody unresolved. `emotive-setup-interactive/SKILL.md` is a fourth
   file and takes only what is different about asking — it defers to
   `emotive-setup` for the procedure, so a rule belonging to the install itself
   goes there and is inherited. Two copies of a step is the failure mode; the
   interactive skill is a diff. What it may ask about is bounded: the local half,
   never the glossary, which no skill installs and so no skill can subset.
2. Run `./build.sh` — it re-pads the glossary in `context/session-titles.md` and
   copies it into the README between `glossary:begin` / `glossary:end` markers.
   Never hand-edit that block; it will be overwritten. The build fails on a
   `<placeholder>` in the glossary, because that file ships as-is: a placeholder
   there would reach every session unresolved. Tailored wording belongs in
   `template.md`.
3. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json`
   for anything that should ship. Without a bump, `claude plugin update` reports
   "already at the latest version" even when `main` has new commits. `/ship`
   does this. An install delivers `plugins/emotive/` and nothing else, so a
   change entirely outside it — this file, the README, the repo's own skills —
   lands without a bump and without a tag: bumping would announce an update that
   changes nothing on an installed machine. That is about the plugin channel
   alone. The README is the package's front page and is public the moment it
   lands on `main`, which is where someone deciding whether to install reads it.

The skill half can be symlinked — `~/.claude/skills/emotive-setup` pointing at
`plugins/emotive/skills/emotive-setup` makes an edit live in the next session
with no install step. The glossary half cannot: the hook only runs for an
installed plugin, so a machine with the symlink and no plugin gets the skill and
no injected glossary, which is the one combination that reads as the convention
being broken. Install the plugin and drop the symlink; `claude --plugin-dir
plugins/emotive` loads the working tree for one session when an edit needs
trying before it ships. A newly added skill is not live locally until the branch
that adds it lands and the plugin updates, so try it with `--plugin-dir` rather
than waiting for it to appear.

Landing that bump on `main` tags the release from CI. Never tag by hand: a cloud
session cannot push `refs/tags/*` at all, so a tag step in the local workflow is
one more thing that silently only works from a laptop.

`build.sh` builds the README's stripped rows as a separate list rather than
mutating the ones it parsed, so a later check that needs the source's own
spelling of a prefix reads the unmutated rows. An earlier coverage check read
them after the strip and matched nothing; keep the two lists distinct rather
than re-learning that.

An emoji is two columns wide, and a variation selector is zero but widens the
character before it, so a markdown table padded by character count comes out
ragged. `build.sh` pads the glossary by display width in both places it appears,
so neither is ever typed; a table written by hand elsewhere in this repo needs
the same arithmetic, or prettier's.

The README shows a prefix as a bare emoji — 📚, never `📚 `. The code formatting
and the trailing space belong to `context/session-titles.md`, where a session
reading it is about to set a title; `build.sh` strips them from the README's copy
of the table, which is why the two are padded separately, and prose in the README
follows the same rule.

`AskUserQuestion` caps a question at four options and a call at four questions,
so a wide ask is grouped `multiSelect` questions in a single call. One call
renders as one dialog; three calls would be three interruptions.

## The vocabulary is shared, not invented here

The glossary came from the sibling repos that use it — `karabiner` and `axshot`
first, then `github-triage`, `email-filter-builder`, `a-thousand-worlds`,
`regard` and `blunt` — and their `AGENTS.md` files still hold field copies of
it. `~/projects/karabiner/docs/workflow.md` holds the original reasoning.

Those copies are what the hook makes unnecessary, and the sweep that removes
them is a subtraction, not a rewrite: cut the rows and the generic prose, keep
only what answers the repo — its gate, its branch, its shared resources. Until a
repo has been swept it carries the glossary twice, which costs tokens and reads
as duplication but conflicts with nothing.

Once swept, changing the meaning of a prefix costs one edit here instead of a
pass over every repo. That is the whole reason the glossary moved into a hook.
Adding a row costs nothing anywhere, since an unused row is inert by design.

Say a prefix is **set in the response that enters the stage** — never "by hand",
which reads as something the user does when every setter is an agent. The
distinction the wording has to carry is whether a skill owns the stage and
re-reads it each run, or a response sets it inline from an instruction it has to
remember.

Name a stage by what happens in it, never by a skill only this machine has. The
`📚 ` row said "the response that invokes `learn`", and `learn` is a user-level
skill nobody else has — so the injected copy names the pass instead, and reads the
same in any project.

## Evaluating a change

Do not reason about what an instruction "would" cause and call that a result.
Run the skill against a real repo — a scratch `git init` for the blank-project
path, a clone of a sibling repo for the revise-in-place path — and read what it
wrote. A section that reads well here and lands badly there is the failure this
repo exists to catch. A clone carries only the skills a repo commits: a
gitignored `.claude/` clones away the half that matters most.

Check both halves: the section in the instructions file, and the skills the
install claims to have wired. A `🚀 ` row with no owner is the bug that keeps
coming back; its mirror is a skill nobody wrote down, so grep every skill file
for the prefix characters rather than trusting prose about which skill sets
what — the prose goes stale and the skill is what runs.

For the injected half, `claude --plugin-dir plugins/emotive` loads the working
tree for one session, so a run with the flag and a run without differ by exactly
that. Read what the session actually did with its title, not what the file says
it should have. A plugin `SessionStart` hook must print to stdout — its JSON
`hookSpecificOutput.additionalContext` is dropped for plugins and reported as a
success (anthropics/claude-code#16538), so a hook that looks correct and reaches
nobody is the failure mode to watch for.

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

The glossary arrives from this plugin's own `SessionStart` hook, so it is not
repeated here. These are the parts specific to this repo.

- `📦 ` means `./build.sh` ran clean and the version is bumped — shippable
  without re-running anything.
- `🚀 ` ships to `main`, which the marketplace serves; `/ship` is that procedure
  and sets the prefix itself, as its step 0.
- `🚙 ` is what this repo waits on a user for: a decision about the convention,
  or a review of a branch.
- `💾 `, `🔍 `, `🔒 ` and `🔓 ` are inert here — nothing in this repo is shared
  across sessions. They still arrive in every session, which is the rule this
  repo teaches; a repo that made an exception of itself would be arguing against
  its own product.
- `📚 ` is for extracting learnings into this file, the README or the skills.
  Nothing else sets it, so the response that starts that pass puts it on.

A session in this repo is reading the glossary it is editing, and only if the
plugin is installed on the machine — the symlink alone does not bring the hook.
A change to a rule takes effect in the *next* session, or in one started with
`--plugin-dir plugins/emotive`, never retroactively in the session that wrote it.
