---
name: emotive-setup-interactive
description: >
  Set up the session-title emoji status convention in the current project, asking
  the user which prefixes to install instead of shipping the whole glossary. Use
  when the user invokes /emotive-setup-interactive or says "let me pick the
  prefixes", "ask me which emoji to install", "set up emoji status interactively".
---

The install is `emotive-setup`'s, beside this one. Read `../emotive-setup/SKILL.md` and the
`../emotive-setup/template.md` it points at, and run that procedure. This skill changes exactly one
thing about it: **which rows land in the table is the user's answer, not a fixed glossary.**

Everything else is unchanged — where the section goes, what the rows are tailored with, which skills
get wired, how it lands. Tailoring is read from the repo as it always was; the user is asked which
stages this project has, not what its branch is called.

## 0. `⏳ ` first, before the ask

`emotive-setup`'s step 0, verbatim: `mcp__ccd_session_mgmt__get_session` with `"self"`, then
`set_session_title` with `⏳ ` in front. Before the multiselect, not after — the prefix on this
session is the convention this skill carries, not a preview of what the project chose. It does not
change with the answer, and it is not mentioned in the response.

## 1. Read what is already there

Before asking, find the instructions file (`emotive-setup` step 1) and read two things:

- **A session-titles section, if the project already has one.** Then the ask is about changing
  membership, and the questions say which rows are already installed.
- **What the repo's own skills already set.** A prefix a skill sets is not droppable: the skill is
  the thing that runs, and instructions that omit it are wrong about the repo.

  Find them by grepping every skill file for every prefix character — all twelve, one grep — not by
  reading the instructions' prose about which skills set what. Those two disagree, which is half of
  why this convention wants installing: a `test` skill that ends by setting `📦 ` is invisible to a
  section that only mentions the lock and the ship. Do this **before** writing the questions, so
  each undroppable prefix's description can lead with *always installed, `<skill>` sets it*. An
  option offered as a checkbox and then overridden makes the dialog a lie, and the user finds out
  in the report.

## 2. Ask, in one call

One `AskUserQuestion` call, three `multiSelect` questions, four prefixes each. The tool caps a
question at four options, so "all twelve at once" is three groups in one dialog — not three dialogs,
and not four questions with the fourth spent on something else. Twelve options in one call is the
whole ask.

| Question | Prefixes |
| --- | --- |
| Making something | `🎨 ` designing · `⏳ ` implementing · `📦 ` done on the branch · `🚀 ` shipping |
| Sharing with other sessions | `🔍 ` auditing live state · `🔓 ` queued for the slot · `🔒 ` holding it · `💾 ` writing live |
| Waiting, and after | `🚙 ` parked on the user · `⏲️ ` waiting on a schedule · `🪦 ` dead end · `📚 ` learnings |

Each option's label is the emoji and the stage; its description is what the project loses by leaving
it out, in the project's own terms — the `💾 ` description names the repo's live resource, the `🚀 `
description names its default branch. A description that could have been written without reading the
repo is a wasted question.

Say in the question text that leaving a prefix checked costs a line and nothing else, and that an
unchecked one is left out of the table entirely. The default answer is all twelve; a prefix is
dropped because the project will never reach the stage, not because it has not reached it yet.

All twelve are shown even where some cannot be dropped — the dialog is the glossary, and a project's
stages read differently next to the ones it does not have. What a skill owns is said in the
description, not withheld from the list.

**An empty selection is a refusal.** Nothing is installed, no file is touched, and the response says
so in a line. Do not write a section with an empty table.

## 3. Install the subset

`emotive-setup` steps 2 through 5, with the dropped rows simply absent — from the table, and from
the paragraphs under it, which have nothing left to attach to.

- **`🚀 ` dropped** takes step 3's `ship` wiring and step 3a's minimal `ship` skill with it. A
  project with no shipping row does not get a skill written to set one. Say so in the report; it is
  the one drop that leaves the repo with less than it would otherwise have had.
- **A hazard dropped** (`💾 `, `🔍 `, `🔒 `/`🔓 `) drops its paragraph too. That much is already how
  `template.md` works — a paragraph with nothing to say is cut. Its mentions elsewhere are not.
- **`📚 ` dropped** drops the sentence that says a `learn` skill does not set its own prefix.

**Then grep the instructions file for each dropped prefix character and fix every hit.** The table
row is the easy half. A prefix also turns up in the paragraph that calls two of them inert, in a
precedence rule that ranks it against another, and in sections nowhere near the glossary — a
cloud-session note listing the stages a container cannot reach, a skill's own procedure. A dropped
prefix left standing in prose is worse than the row would have been: the row was inert, and the
sentence is now wrong. Rewrite the survivors rather than deleting the sentence when it still has
something to say about the prefixes that stayed.

## 4. Mark it as chosen

When fewer than twelve rows land, the installed section says so, in place of the "whole glossary
ships" paragraph `template.md` carries. The wording is in that file, under **When the glossary is a
chosen subset**.

That line is what keeps the two skills from fighting: `emotive-setup` revises a section that says it
is a subset as it stands, correcting rows rather than filling in the ones it left out. Membership is
this skill's; wording is `emotive-setup`'s. A project that wants the rest of the glossary back runs
this skill again and checks them.

## 5. Report

`emotive-setup`'s report, plus the two lines only this skill can give: which prefixes the project
kept, and which it dropped. Name the dropped ones — a reader of the report should not have to
subtract the table from the glossary to see what happened. An unchecked prefix that a skill forced
back in is its own line, with the skill named: the user asked for it to go and it did not.
