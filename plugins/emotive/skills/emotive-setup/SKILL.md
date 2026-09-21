---
name: emotive-setup
description: Wire the session-title lifecycle prefixes into the current project — the repo-specific half of the convention in its agent instructions, and the prefix changes the glossary promises wired into the skills that own them. Pass --ask to be asked about the parts it would otherwise infer. Use when the user invokes /emotive-setup or says "set up emoji status", "adopt the session title prefixes here", "wire the emoji lifecycle into this repo", "ask me about the emoji setup", "don't guess my shared resources".
argument-hint: "[--ask]"
---

# Emotive setup

**The glossary is not installed by this skill.** The plugin's `SessionStart` hook prints
`context/session-titles.md` into every session, so the twelve rows and every rule that reads the
same in every repo are already in front of you — reread them there rather than restating them.
What this skill installs is the half a hook cannot know: what those rows mean *in this repo*, and
the prefix changes wired into the skills that own them.

`template.md`, beside this file, carries the shape of that local half. Read it before writing
anything.

**`--ask` changes who decides, and nothing else.** Without it, step 3 reads the repo and writes the
most likely answer. With it, step 3 shows what it found and the user picks. Same questions either
way; a guess versus an answer. It cannot change which rows exist — the hook delivers the glossary
whole, so there is no membership to set, and nothing here asks about one.

$ARGUMENTS

## 0. Put `⏳ ` on this session first

`mcp__ccd_session_mgmt__get_session` with `"self"` for the id and title, then `set_session_title`
with `⏳ ` in front of the existing title, replacing any prefix already there. Do it before reading
the repo, not after the files land — a convention whose own installer does not follow it will not
survive its first week. Say nothing about it in the response, then or ever.

`⏳ `, not `📚 `: this is implementation that happens to write instruction files, and `📚 ` is for a
session lifting its own findings into them. The prefix says what the session is doing, not which
paths it edits.

## 1. Confirm the glossary actually arrives

The wiring is half a convention on its own: a `ship` skill that sets `🚀 ` means nothing in a
session that was never told what `🚀 ` is. So check that the hook is live — the injected
`## Session titles` block is in your own context if it is.

If it is not, the plugin is not installed on this machine. Say so and give the install, then wire
the repo anyway; the two halves are independent and the wiring keeps until the hook arrives.

```sh
claude plugin marketplace add raineorshine/emotive
claude plugin install emotive@emotive
```

## 2. Find the one home for instructions

- **`AGENTS.md` exists** — it is the home.
- **Only `CLAUDE.md` exists** — use `CLAUDE.md`. Do not split the repo's instructions across two
  files to match a convention it has not adopted.
- **Neither exists** — create `AGENTS.md` with a one-line description of the project above the
  section, and a `CLAUDE.md` holding `@AGENTS.md` and a line saying instructions live there and are
  shared with other agent tools. This is the blank-project path, and it is the whole of it: do not
  scaffold anything else.

A repo whose instructions live somewhere else again — `.cursorrules`,
`.github/copilot-instructions.md` — gets the section in the file the repo actually maintains, not a
new one.

A blank project may well have nothing local to say yet. Then the section is one line naming where
the glossary comes from, or nothing at all — an empty section is worse than none, and the hook has
already done the work.

## 3. Read the repo for the local half only

Every one of these is a thing the injected copy states generically and this repo can state exactly.
Answer them from the repo, and write down only the ones that have an answer:

- **What ships, and to where.** The default branch name, and whether it ships through PRs or
  fast-forwards. `🚀 ` says "the default branch"; the repo can name it.
- **What the gate is** — the test, lint and build commands that make a branch `📦 `. No gate at all
  is an answer: `📦 ` then means done on the branch, and the section says so rather than implying a
  check that does not exist.
- **What is shared across worktrees.** This is the part that earns the section its place, and the
  only one another session acts on. A live database, a real account, a deploy target, a dev-server
  port, a device, a config file outside the repo — anything two sessions can race on. Name the
  scripts that write to it, and write the paragraph `template.md` pairs with `💾 `, `🔍 ` or
  `🔒 `/`🔓 `. A repo with nothing shared writes none of this and says nothing; the rows stay inert
  in the injected copy, which is where they live now.
- **What the repo waits on the user for** — a credential, an OAuth click, a look at a branch.
- **What a cloud session cannot do here** — credentials outside git, a push convention the cloud
  harness refuses, hardware it does not have. If any stage is out of reach, say which and why, and
  where a cloud session ends instead.

Every one of those is found by reading what the repo contains — its scripts, its skill files, its
`package.json`, its CI config — never by looking for prefix characters in it. **A project adopting
this convention has no emoji in it yet**, so a grep for the twelve prefixes is empty by definition
on a first install and cannot be what the answers come from. That grep has exactly one job, in step
4: on a repo that has already adopted the convention, it finds which skills are already setting
something. Expect nothing from it otherwise, and never let an empty result read as "this repo shares
nothing".

### With `--ask`, show what you found instead of picking

One `AskUserQuestion` call, three `multiSelect` questions. The tool caps a question at four options,
so each offers at most four of what the reading above turned up — and the options are things from
this repo, with their paths, never prefixes:

| Question | Options come from | The answer writes |
| --- | --- | --- |
| What do sessions here race on? | scripts that write outside the worktree, a lock file, a fixed port, an `.env` outside git, an install or deploy target — plus "nothing is shared" | the `💾 ` / `🔍 ` / `🔒 ` hazard paragraph, or the line that says those rows are inert here |
| What makes a branch done? | the test, lint and build commands that exist — a `test` skill, a `package.json` script, a build script, CI — plus "no gate" | the `📦 ` line, naming the command verbatim |
| Which skills should set a prefix? | the skill files that exist, each labelled with the prefix it would own | the wiring in step 4, and whether a `ship` skill gets written |

Each option's description says what the project gets by choosing it, naming the script or command. A
skill that **already** sets a prefix leads its description with *already sets `<prefix>`* — that one
is not really a choice, since the skill is what runs, and an option offered then overridden makes
the dialog a lie.

**"Nothing is shared" is a real answer, not a refusal.** It writes the one-line inert version, which
is what `template.md` asks for in a repo with nothing to warn about. An empty answer to all three is
a refusal: nothing is installed, no file is touched, and the response says so in a line.

**An option left unchecked is not written**, however good the case for it looked while the options
were being drafted. Offering one and then installing it anyway is the same defect as offering one a
skill already overrides — the dialog stops being the thing that decided. Where leaving it out costs
the repo something, say so in the report rather than in the file.

Where there is nothing to choose between — one candidate and no ambiguity — write it and do not ask
about it. A dialog whose every question has one option is a confirmation prompt, not a choice.

**Never re-list the glossary.** A row copied into the repo is a row that goes stale the next time
the plugin ships one, and the sweep this plugin exists to end starts again. Name a prefix only to
say something about it that is true here and nowhere else.

Fit the section to the repo's existing voice and line width, and keep it inside whatever budget the
instructions file is already held to.

## 4. Wire the skills that own a prefix

This is the half that does not go stale, because a skill rereads its own procedure every run while
an injected rule competes with everything else in context. Edit the skills the repo already has, and
write the one missing skill that `🚀 ` cannot do without (step 4a).

- **`ship`** — set `🚀 ` as the **last** step, once the push has landed, replacing whatever prefix
  was there rather than stacking. Not before: `🚀 ` names a result, so a ship that rebases, re-tests
  or retries a rejected push keeps the prefix that was already true — usually `📦 `, which holds
  through the ship. That is also why this wiring needs no restore path: a ship that falls over never
  set it. Once landed, `🚀 ` stays until another stage replaces it, and is never cleared to leave a
  bare title. Say nothing about it.
- **The skill that takes a lock**, where there is one — `🔓 ` before the acquire, `🔒 ` once it
  reports acquired, `🔓 ` before the release, then `📦 ` or whatever stage the branch actually
  reached. Each with "say nothing about it".
- **`learn`** — usually the user-level one, which this skill does not touch, and which sets no
  session title at all. The injected copy already says `📚 ` goes on in the response that invokes it.
  Repeat that in the repo's instructions only if the repo has its own `learn` skill to point at.

A missing `test` skill is not this skill's job: a repo with no gate has no `📦 ` to own, and step 3
records that instead.

Where a repo's skill already sets a prefix, keep the emoji it uses and reconcile the other way —
match the section to the skill, because the skill is the thing that runs. Find those by grepping
every skill file for the twelve prefix characters, rather than trusting the instructions' prose
about which skill sets what: the prose goes stale and the skill runs. On a first install this finds
nothing, which is the expected result and not a finding — it is only a re-run or a sweep that has
anything to reconcile.

## 4a. When there is no `ship` skill, write a minimal one

Every project ends up with a `ship` skill; a new one just has not written it yet. So a repo without
one gets it here rather than a `🚀 ` that waits on a skill nobody has scheduled — it is the one
prefix whose whole value is that nobody has to remember it. Minimal means minimal: the prefix, the
repo's own gate, the commit, and the landing it already does. It is not a port of another repo's
ship skill, and it does not introduce a workflow the repo has not chosen.

Derive the landing shape from the repo, and say in the skill which one it is:

- **Merge commits or a PR in the history, or a remote with branch protection** — commit, push the
  branch, open the PR. An open PR is not a landing, so the skill ends at `📦 `; `🚀 ` waits for the
  merge.
- **A linear history on the default branch** — commit, rebase, fast-forward, push.
- **No remote at all** — commit on the default branch and stop. Still worth a skill: it owns the
  gate and the prefix.

The `🚀 ` is the skill's last step, after the landing, as in step 4 — not its first, and with no
restore path, since a ship that does not land never set it. Say nothing about it, and point at the
injected glossary rather than restating it.

Where the repo keeps its skills is wherever it already keeps them — `.claude/skills/ship/SKILL.md`
unless a `.github/skills/` tree exists. A blank project gets `.claude/skills/`.

## 5. Revise in place, never append a second copy

A repo that already has a session-titles section gets it corrected, not duplicated.

### A repo may narrow a row, never invert one

Naming this repo's gate, branch or shared resource narrows a row and is the whole point. Changing
what a prefix *means* inverts it, and costs the convention the only thing it is for — a sidebar that
reads the same from one repo to the next.

Some field copies invert one anyway, with an argument attached — and one of them won. `regard` held
that `🚀 ` means *shipped* and must not go on until the push lands, against a glossary that then said
to set it optimistically; the glossary changed to match. So a repo's reasoned departure is evidence
about the convention, not a defect in the repo.

So where a repo contradicts the injected glossary, do not resolve it either way. Leave the skill
that implements the repo's version alone, and write the departure into the section as a departure —
which row, what it means here instead, that it is deliberate, and where the reasoning lives. A
reader then sees one rule and one stated exception, rather than two rules and no way to tell which
is load-bearing. Say it in the report too: it is a decision for whoever owns the repo, and nothing
here is the place to make it. Everything else in that repo still gets swept and wired as usual.

### The pre-hook sweep

A repo carrying the **old, pre-hook full copy** — the whole twelve-row table and the generic prose —
is the common case now, and the fix is subtraction: cut every row and every paragraph the injected
copy already states, and keep only what answers step 3. If nothing is left, cut the section. Say in
the report which repo lost its table, so the change reads as the sweep it is rather than as a
deletion nobody asked for.

## 6. Land it

Follow the repo's own `ship` skill if it has one; otherwise commit. Commit only the instruction
files and the skills this skill edited, and say what was left behind — never sweep the rest of the
working tree in. A project that is not a git repo gets the files and a line saying nothing was
committed.

Report what landed: which file carries the local half and what it says, which skills now set a
prefix, whether a `ship` skill was written, and whether the glossary hook is live on this machine.
Never report the prefix on this session's own title.
