# AGENTS.md

Emotive: a session-title status convention for Claude Code, shipped as a plugin
named `emotive`. The product is in two halves. The **glossary** —
`context/session-titles.md` — is injected into every session by a `SessionStart`
hook, so it needs no per-repo install and is identical everywhere — the plugin
itself still has to be installed on the machine. The **wiring** — the
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
| `.claude/skills/ship/SKILL.md` | the ship procedure — build, rebase, version, land, tag, learn |
| `.claude/skills/update/SKILL.md` | `/update <prompt>` — routes a change to the right half |
| `build.sh` | normalizes the glossary table and copies it into the README |
| `.github/workflows/tag-release.yml` | tags `v<version>` when a bump lands on `main` |
| `docs/glossary.md` | wording rules for the injected file, and what the build does to its table |
| `docs/evaluating-a-change.md` | how to find out what an edit actually does before shipping it |
| `docs/hook-injection.md` | how the injection works, what is broken about it, what was rejected |
| `docs/sweep.md` | running the sweep against a sibling repo, and what it can destroy |

## Editing the convention

**Pick the half first.** `context/session-titles.md` holds what every session
reads — the rows, and every rule that is true regardless of repo. `SKILL.md` holds
what an installing session does. `template.md` holds the shape of what lands in one
repo's instructions. A rule in the wrong half either reaches nobody or reaches
everybody unresolved. The test for the first half: would this sentence read the
same in a repo you have never seen? If a rule bound for the shipped convention names
a command, a branch or a resource, it belongs in `template.md`.

**`docs/` does not ship.** An install delivers `plugins/emotive/` alone, so a rule
kept only in a doc is invisible to the session running the skill — which is how
`SKILL.md` came to tell a sweep to run the target's gate while `docs/sweep.md`
forbade it. Where a doc overrides a shipped step, the override belongs in the step;
the doc keeps the reasoning.

Asking is a branch inside `SKILL.md`, reached by `--ask`, not a second skill
deferring to the first: two documents describing one procedure have to be kept
agreeing, and that agreement is not checkable. As a branch, a change to a step is
one edit. What the ask may cover is bounded to the local half, never the glossary,
which no skill installs and so none can subset.

**Nothing the skill decides may come from grepping a repo for prefix characters.**
A project adopting this convention has no emoji in it yet, so that grep is empty by
definition on a first install, and empty must never read as an answer. Candidates
come from what the repo contains — its scripts, its skill files, its `package.json`,
its CI config. The grep's one job is reconciling a repo that already adopted the
convention.

**Run `./build.sh`** — it re-pads the glossary in `context/session-titles.md` and
copies it into the README between `glossary:begin` / `glossary:end` markers. Never
hand-edit that block or hand-pad a table; see [docs/glossary.md](docs/glossary.md)
for the wording rules and what the build does.

**Then verify it against a real repo**, rather than reasoning about what the
instruction would cause — [docs/evaluating-a-change.md](docs/evaluating-a-change.md).

## Shipping

`/ship` owns the procedure: build, commit, rebase, version bump, land on `main`,
confirm the CI tag. Bump the minor version in
`plugins/emotive/.claude-plugin/plugin.json` for anything that should ship — without
one, `claude plugin update` reports "already at the latest version" even when `main`
has new commits. An install delivers `plugins/emotive/` and nothing else, so a
change entirely outside it lands without a bump or a tag; the README still goes
public on `main`.

Nothing locks `main`, and two sessions in two worktrees can reach `/ship` at once.
The failure is not the conflict, which git refuses safely; it is the version number,
which the skill derives off `origin/main` *after* the rebase for that reason. Never
tag by hand either: a cloud session cannot push `refs/tags/*` at all, so a tag step
in the local workflow is one more thing that silently only works from a laptop.

This machine runs the installed plugin, not a symlink into the repo, so an edit here
is not live until `/ship` lands it and `claude plugin update` fetches it.
`claude --plugin-dir plugins/emotive` loads the working tree for one session to try
it first. Never a symlink *and* the plugin — see
[docs/hook-injection.md](docs/hook-injection.md) for the one broken combination.

## The vocabulary is shared, not invented here

The glossary came from the sibling repos that use it — `karabiner` and `axshot`
first, then `github-triage`, `email-filter-builder`, `a-thousand-worlds`, `regard`
and `blunt`. `~/projects/karabiner/docs/workflow.md` holds the original reasoning.
All but `blunt` have been swept; `blunt/AGENTS.md` still holds a field copy of the
table.

A field copy is what the hook makes unnecessary, and cutting one is a subtraction,
not a rewrite: cut the rows and the generic prose, keep only what answers the repo.
Until a repo is swept it carries the glossary twice, which costs tokens but
conflicts with nothing. Once swept, changing the meaning of a prefix costs one edit
here instead of a pass over every repo — that is the whole reason the glossary moved
into a hook. [docs/sweep.md](docs/sweep.md) has the procedure and what it can
destroy, starting with never running the target's own `ship` skill.

**A repo may narrow a row, never invert one — but `regard` won the argument about
`🚀 `.** It held that the prefix names a result and must not go on until the push
lands, against a glossary that said to set every prefix optimistically; the glossary
changed to match rather than overruling it. So the divergence has flipped: the repos
to fix in a sweep are the ones still setting `🚀 ` before their gates. A reasoned
inversion is evidence about the convention — read the argument before assuming the
repo is wrong.

## Git

Conventional-commit subjects (`docs:`, `feat:`, `fix:`), matching the history. Work
happens on a branch in a worktree and lands on `main`; `/ship` is that procedure.

A session that arrived here mid-session — started on no folder, or moved in with
`change_directory` — never registered this repo's `.claude/skills`, so `/ship` and
`/update` answer "Unknown skill" for the rest of its life. The files are committed and
present in every worktree; it is the skill list that is stale, not the checkout. Run the
steps out of `.claude/skills/ship/SKILL.md` directly rather than reading the failure as a
missing or gitignored skill — and re-read that file each time, since it is the procedure,
not a description of one.

## Reporting

Never suggest restarting Claude Code. After `/ship`, end on the last bullet — no
"Done.", no "Ready to …"; nothing is waiting on the user.

## Session titles

The glossary arrives from this plugin's own `SessionStart` hook, so it is not repeated
here — and it governs a session working on it like any other. These are this repo's parts.

- `📦 ` means `./build.sh` ran clean and the version is bumped, and it holds right
  through a `/ship` until the push lands.
- `🚀 ` means the push to `main` landed — the marketplace serves `main`, so nothing
  before that is shipped. `/ship` sets it as its last step; `📦 ` holds until then.
- `🚙 ` is a decision about the convention, or a review of a branch.
- `💾 `, `🔍 `, `🔒 ` and `🔓 ` are inert here — nothing in this repo is shared
  across sessions. They arrive anyway; a repo making an exception of itself would
  be arguing against its own product.
- `📚 ` is for extracting learnings into this file, the README or the skills. Only
  that pass sets it, and it goes on in the response that starts one — whether the
  pass is driven by a skill or run inline.

A session here is reading the glossary it is editing. A change takes effect in the
*next* session, or in one started with `--plugin-dir plugins/emotive` — never
retroactively in the session that wrote it.
