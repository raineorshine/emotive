# Injecting the glossary with a SessionStart hook

How the glossary reaches every session, which parts of that mechanism are broken,
and which alternatives were tried and rejected. Read this before changing
`plugins/emotive/hooks/hooks.json` or moving anything between the injected half
and the skill's half.

## What works

`hooks/hooks.json` declares a `SessionStart` hook whose command is
`cat "${CLAUDE_PLUGIN_ROOT}/context/session-titles.md"`. The file's bytes land in
the model's context as the hook's stdout. No matcher, so it fires on every start
reason — including `compact`, which is what puts the glossary back after a
compaction.

This is the shape `blunt` has shipped for 35 minor versions
(`~/projects/blunt/plugins/blunt/hooks/hooks.json`); emotive copied it rather
than inventing one.

The published hooks reference states that `SessionStart` cannot add context to the
conversation. A running session contradicts it, so do not settle that question by
reading the docs.

## The trap: `additionalContext` is dropped for plugins

A hook may instead emit JSON with
`hookSpecificOutput.additionalContext`. **For a plugin hook that field never
reaches the model** — the session sees only `SessionStart:… hook success: Success`.
Filed as [anthropics/claude-code#16538][16538] and closed as not planned; the
documented workaround is to move the hook into `~/.claude/settings.json`, which a
plugin author cannot do for their users.
[#88086][88086] is the same silent failure inside the VS Code extension.

So: **print to stdout, never JSON.** A hook written the JSON way looks correct,
logs as a success, and reaches nobody.

[16538]: https://github.com/anthropics/claude-code/issues/16538
[88086]: https://github.com/anthropics/claude-code/issues/88086

## Rejected alternatives

| Approach | Why not |
| --- | --- |
| `output-styles/` | Exclusive — one active at a time, so shipping the convention as a style makes it an either/or with any other style the user runs. A hook composes. |
| Keeping the glossary in each repo's `CLAUDE.md` | Official guidance points here for instructions that never change, and it is what emotive did before. But `CLAUDE.md` is per-repo, and this is a cross-project convention: it would mean one copy per repo, which is the sweep the hook exists to end. The guidance is about project conventions and does not cover this case. |
| A `Stop` hook that checks the title and nudges | A `Stop` hook can block and continue the turn, but reading the session's current title from a hook means reaching into the app's own state. Too fragile for what it buys. |
| Asking the session to re-derive the local half every start | No file to maintain, but every session pays to re-read the repo, and the answer is not stable between sessions. |

## What the mechanism cannot do

**The hook has no effect where `set_session_title` does not exist.** That tool is
the desktop app's (`mcp__ccd_session_mgmt__*`); a terminal session has no sidebar
and no such tool, so the injected rules are inert there. The injected copy says so
in its last line rather than leaving a session hunting for a tool it does not have.

**An injected rule is weaker than one in a skill.** `blunt`'s own `AGENTS.md`
records that models blend injected style rules with harness guidance and their own
priors, and that several of its bullets are routinely disobeyed. Some counterweight:
the hooks guidance notes that injected message content cannot be deprioritized the
way a file read before the session can. Either way, a rule a skill reads at the
moment it runs is the stronger placement — which is why `🚀 ` is wired into a
repo's `ship` skill as step 0 rather than left to the glossary alone.

**The hook only runs for an installed plugin.** A machine with a
`~/.claude/skills/emotive-setup` symlink and no installed plugin gets the skill and
no glossary — the one combination that reads as the convention being broken. Install
the plugin; use `--plugin-dir` to try an edit before it ships.
