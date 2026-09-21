---
name: emotive-setup-interactive
description: >
  Set up the session-title emoji status convention in the current project, asking
  the user about the parts of it that cannot be read off the repo, instead of
  inferring them. Use when the user invokes /emotive-setup-interactive or says
  "ask me about the emoji setup", "don't guess my shared resources", "set up
  emoji status interactively".
---

The install is `emotive-setup`'s, beside this one. Read `../emotive-setup/SKILL.md` and the
`../emotive-setup/template.md` it points at, and run that procedure. This skill changes exactly one
thing about it: **the parts that procedure infers from the repo become the user's answer.**

Which rows exist is not one of those parts. The glossary arrives in every session from the plugin's
`SessionStart` hook, whole and identical everywhere; there is nothing to install and so nothing to
pick. What is genuinely uncertain is the local half — what this repo shares, what its gate is, which
of its skills should set a prefix — and every one of those is something the non-interactive skill
has to guess from file names and script contents. Guessing wrong writes a hazard paragraph about the
wrong resource, which is worse than no paragraph at all.

## 0. `⏳ ` first, before the ask

`emotive-setup`'s step 0, verbatim: `mcp__ccd_session_mgmt__get_session` with `"self"`, then
`set_session_title` with `⏳ ` in front. Before the questions, not after — the prefix on this session
is the convention this skill carries, not a preview of what the project chose. It does not change
with the answer, and it is not mentioned in the response.

## 1. Read the repo first, so the questions are about it

A question whose options could have been written without opening the repo is a wasted question. Do
`emotive-setup` steps 1 through 3 as written — find the instructions file, and read the repo for the
gate, the branch, the shared resources, the cloud limits. Then, additionally:

- **Grep every skill file for every prefix character** — all twelve, one grep — rather than reading
  the instructions' prose about which skills set what. Those two disagree, which is half of why this
  needs installing: a `test` skill that ends by setting `📦 ` is invisible to a section that only
  mentions the lock and the ship.
- **List the candidates for a shared resource** by what the repo actually contains: scripts that
  write outside the worktree, a lock file, a fixed port, a `.env` outside git, an install target, a
  deploy command. Each becomes an option; none becomes a paragraph until the user says so.

What you find is the option list. What you cannot find is why you are asking.

## 2. Ask, in one call

One `AskUserQuestion` call, three `multiSelect` questions. The tool caps a question at four options,
so each question offers at most four of what step 1 found — the candidates, not the glossary.

| Question | Asks | Writes |
| --- | --- | --- |
| What do sessions here race on? | the shared-resource candidates found in step 1, or "nothing is shared" | the `💾 ` / `🔍 ` / `🔒 ` hazard paragraph, or the line that says they are inert here |
| What makes a branch done? | the test, lint and build commands found, or "no gate" | the `📦 ` line |
| Which skills should set a prefix? | the skills found, each labelled with the prefix it would own and whether it already sets one | the wiring in step 4, and whether a `ship` skill gets written |

Each option's description says what the project gets by choosing it and what it loses by not — in
the project's own terms, naming the script or the command. A skill that **already** sets a prefix
leads its description with *already sets `<prefix>`*, because that one is not really a choice: the
skill is the thing that runs, and an option offered and then overridden makes the dialog a lie.

**"Nothing is shared" is a real answer, not a refusal.** It writes the one-line version — the hazard
rows are inert here — which is exactly what `template.md` asks for in a repo with nothing to warn
about. An empty answer to all three, though, is a refusal: nothing is installed, no file is touched,
and the response says so in a line.

## 3. Install what was answered

`emotive-setup` steps 4 through 6, with the answers in place of the inferences. Nothing about the
glossary changes — it was never this skill's to change, and the section still never restates it.

- **A shared resource chosen** gets its paragraph written from `template.md`'s hazard table, naming
  the script the user picked. This is the one thing in the whole install that another session acts
  on, and the reason this skill exists.
- **A skill chosen to own a prefix** gets wired as in step 4. `🚀 ` unchosen still means no `ship`
  wiring and no minimal `ship` skill written — say so in the report, because it is the one answer
  that leaves the repo with less than `emotive-setup` would have given it.
- **A gate chosen** is named in the `📦 ` line verbatim, as a command someone can run.

## 4. Report

`emotive-setup`'s report, plus the line only this skill can give: which answer produced which
paragraph. A reader should be able to see that the hazard paragraph names `scripts/foo.sh` because
the user picked it, not because a grep guessed. Name any option that was offered and then overridden
by a skill that already sets that prefix, with the skill named.
