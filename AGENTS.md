# AGENTS.md

Emotive: a session-title status convention for Claude Code, shipped as a plugin
named `emotive`. The product is in two halves. The **glossary** —
`context/session-titles.md` — is injected into every session by a `SessionStart`
hook, so it needs no install and is identical everywhere. The **wiring** — the
`emotive-setup` skill, which asks rather than infers when passed `--ask` —
installs only what a hook cannot know: what a row means in one particular repo,
and the prefix changes its own skills have to set. Everything else is packaging.

## Layout

| path | what |
|---|---|
| `plugins/emotive/context/session-titles.md` | the glossary — the actual product, injected into every session |
| `plugins/emotive/hooks/hooks.json` | `SessionStart` hook that cats it into context |
| `plugins/emotive/skills/emotive-setup/SKILL.md` | the wiring procedure — what the skill does when it runs |
| `plugins/emotive/skills/emotive-setup/template.md` | the shape of the local half: what lands in one repo's instructions |
| `plugins/emotive/.claude-plugin/plugin.json` | version; gates `claude plugin update` |
| `.claude-plugin/marketplace.json` | the marketplace listing that serves the plugin |
| `docs/hook-injection.md` | how the injection works, what is broken about it, what was rejected |
| `build.sh` | normalizes the glossary table and copies it into the README |
| `.github/workflows/tag-release.yml` | tags `v<version>` when a bump lands on `main` |

## Editing the convention

1. Pick the half. `context/session-titles.md` holds what every session reads —
   the rows, and every rule that is true regardless of repo. `SKILL.md` holds
   what an installing session does. `template.md` holds the shape of what lands
   in one repo's instructions. A rule in the wrong half either reaches nobody or
   reaches everybody unresolved. Asking is a branch inside `SKILL.md`, reached by
   `--ask`, not a second skill deferring to the first: two documents describing one
   procedure have to be kept agreeing, and that agreement is not checkable. As a
   branch, a change to a step is one edit. What the ask may cover is bounded to the
   local half, never the glossary, which no skill installs and so none can subset.
2. Run `./build.sh` — it re-pads the glossary in `context/session-titles.md` and
   copies it into the README between `glossary:begin` / `glossary:end` markers.
   Never hand-edit that block; it will be overwritten. The build fails on a
   `<placeholder>` in the glossary, because that file ships as-is: a placeholder
   there would reach every session unresolved. Tailored wording belongs in
   `template.md`.
3. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json` for
   anything that should ship; `/ship` does this. Without a bump, `claude plugin
   update` reports "already at the latest version" even when `main` has new
   commits. An install delivers `plugins/emotive/` and nothing else, so a change
   entirely outside it lands without a bump or a tag — bumping would announce an
   update that changes nothing installed. The README still goes public on `main`.

This machine runs the installed plugin, not a symlink into the repo, so an edit
here is not live until `/ship` lands it and `claude plugin update` fetches it. To
try one before it ships, `claude --plugin-dir plugins/emotive` loads the working
tree for a single session. Never a symlink *and* the plugin — see
[docs/hook-injection.md](docs/hook-injection.md) for why the symlink alone is the
one broken combination.

Landing that bump on `main` tags the release from CI. Never tag by hand: a cloud
session cannot push `refs/tags/*` at all, so a tag step in the local workflow is
one more thing that silently only works from a laptop.

An emoji is two columns wide, and a variation selector is zero but widens the
character before it, so a table padded by character count comes out ragged.
`build.sh` pads the glossary by display width in both places it appears, so
neither is ever typed; a table written by hand elsewhere needs the same
arithmetic, or prettier's.

The README shows a prefix as a bare emoji — 📚, never `📚 `. The backticks and the
trailing space belong to `context/session-titles.md`, where a session reading it
is about to set a title. `build.sh` therefore keeps two row lists — the parsed
ones and the stripped ones — padded separately; a check wanting the source's own
spelling must read the unmutated list, which is what an earlier coverage check got
wrong when it matched nothing. README prose follows the bare-emoji rule too.

`AskUserQuestion` caps a question at four options and a call at four, so a wide
ask is grouped `multiSelect` questions in one call — one dialog, not three
interruptions.

**A project adopting this convention has no emoji in it yet.** So nothing the
install decides can come from grepping a repo for prefix characters — on a first
install that grep is empty by definition, and an empty result must never read as
an answer. Candidates come from what the repo contains: its scripts, its skill
files, its `package.json`, its CI config. The prefix grep has one job,
reconciling a repo that has already adopted the convention, and finding nothing
is its normal result.

## The vocabulary is shared, not invented here

The glossary came from the sibling repos that use it — `karabiner` and `axshot`
first, then `github-triage`, `email-filter-builder`, `a-thousand-worlds`, `regard`
and `blunt`, whose `AGENTS.md` files still hold field copies.
`~/projects/karabiner/docs/workflow.md` holds the original reasoning.

Those copies are what the hook makes unnecessary, and the sweep that removes them
is a subtraction, not a rewrite: cut the rows and the generic prose, keep only
what answers the repo. Until a repo is swept it carries the glossary twice, which
costs tokens but conflicts with nothing.

Once swept, changing the meaning of a prefix costs one edit here instead of a
pass over every repo. That is the whole reason the glossary moved into a hook.
Adding a row costs nothing anywhere, since an unused row is inert by design.

**`regard` inverts `🚀 ` on purpose** — its instructions and its `ship` skill both
say the prefix means *shipped* and must not go on until the push lands, against
the glossary's rule of setting it optimistically. A sweep must not flip that: a
repo may narrow a row, never invert one, and where it has inverted one the fix is
to write the departure down as a departure and leave the decision to whoever owns
the repo.

Say a prefix is **set in the response that enters the stage** — never "by hand",
which reads as something the user does when every setter is an agent. The
distinction the wording has to carry is whether a skill owns the stage and
re-reads it each run, or a response sets it inline from an instruction it has to
remember.

Name a stage by what happens in it, never by a skill only this machine has. The
`📚 ` row said "the response that invokes `learn`", and `learn` is a user-level
skill nobody else has — so the injected copy names the pass instead, and reads the
same in any project.

## Shipping while another session is shipping

Nothing locks `main`, and two sessions in two worktrees can reach `/ship` at once. The
failure is not the conflict, which git refuses safely; it is the **version number**. A bump
chosen before the rebase is a bump off a stale `main`, and if the other session lands
first, its tag already holds that number. Derive it from
`git show origin/main:…plugin.json` after the rebase — not from the working tree, which an
abandoned attempt may have bumped already. `/ship` does it in that order.

`main` also moves *during* a resolve, so re-check immediately before the push. Four rebases
in one ship is a normal day when another session is active, and each is cheap; a
force-push to `main` to avoid one never is.

Where the other session has taken the product somewhere incompatible — a different design,
not a textual conflict — that is not a merge to resolve. Say what each side did, put the
choice to the user, and fold in what their answer keeps. `--ask` came out of exactly that:
a concurrent session built a second skill to pick which rows to install, the hook made
that meaningless, and it survived by being pointed at the local half instead.

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
it should have — the published hooks reference is wrong about whether
`SessionStart` can inject at all, so neither it nor the file settles the question.
[docs/hook-injection.md](docs/hook-injection.md) has the mechanism, the
`additionalContext` trap that fails silently, and the alternatives already
rejected.

`--ask` cannot be tested unattended: a headless run has nobody to answer the
dialog, so `-p` exercises the inferring path only. Test the ask by hand, and read
whether its options came from the repo's own files.

## Testing your own output

This convention governs the session that runs it too: a session working here
carries a prefix like any other, and never mentions it.

## Reporting

Never suggest restarting Claude Code. After `/ship`, end on the last bullet — no
"Done.", no "Ready to …"; nothing is waiting on the user.

## Git

Conventional-commit subjects (`docs:`, `feat:`, `fix:`), matching the history. Work
happens on a branch in a worktree and lands on `main`; `/ship` is that procedure.

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
  across sessions. They arrive anyway; a repo making an exception of itself would
  be arguing against its own product.
- `📚 ` is for extracting learnings into this file, the README or the skills.
  Nothing else sets it, so the response that starts that pass puts it on.

A session here is reading the glossary it is editing. A change takes effect in the
*next* session, or in one started with `--plugin-dir plugins/emotive` — never
retroactively in the session that wrote it.
