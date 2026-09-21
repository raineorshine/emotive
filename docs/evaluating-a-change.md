# Evaluating a change

How to find out what an edit to either half actually does, before shipping it.
Read this before concluding that a change to the glossary or to `emotive-setup`
works.

## Run it, do not reason about it

Do not reason about what an instruction "would" cause and call that a result. Run
the skill against a real repo — a scratch `git init` for the blank-project path, a
clone of a sibling repo for the revise-in-place path — and read what it wrote. A
section that reads well here and lands badly there is the failure this repo exists
to catch. A clone carries only the skills a repo commits: a gitignored `.claude/`
clones away the half that matters most.

## Check both halves

The section in the instructions file, and the skills the install claims to have
wired. A `🚀 ` row with no owner is the bug that keeps coming back; its mirror is a
skill nobody wrote down, so grep every skill file for the prefix characters rather
than trusting prose about which skill sets what — the prose goes stale and the
skill is what runs.

## The injected half needs `--plugin-dir` and a distinctive wording

`claude --plugin-dir plugins/emotive` loads the working tree for one session. The
installed plugin loads as well, so a run without the flag is no control — verify an
edit by making its wording distinctive and checking that wording arrives.

Arrival and obedience are two checks, and a failure looks the same from outside.
Ask the session to quote the wording back: that settles whether the hook delivered
it — and if it did not, check `hooks.json` before the glossary, since a hook emitting
JSON logs a success and reaches nobody. Then read what the session actually did with its title — an injected rule is
weaker than one in a skill, and some are routinely disobeyed, so a rule that arrived
and was ignored is a placement problem, not a hook problem. The published hooks
reference is wrong about whether `SessionStart` can inject at all, so neither it nor
the file settles the first question.
[hook-injection.md](hook-injection.md) has the mechanism, the silent
`additionalContext` trap, and the alternatives already rejected.

## `--ask` cannot be tested unattended

A headless run has nobody to answer the dialog, so `-p` exercises the inferring
path only. Test it by hand, and read where its options came from.
