---
name: update
description: Update the emotive-setup skill per a given prompt. Use when the user invokes /update <prompt>.
argument-hint: <prompt>
---

# Update

Set the session title prefix to ⏳ before editing (see Session titles in AGENTS.md). Say nothing about it.

Apply this instruction to the skill:

$ARGUMENTS

Put it in the right half — there are three, and a rule in the wrong one either reaches nobody or
reaches everybody unresolved. (A rule about working on *this* repo is none of the three; it goes in
`AGENTS.md`, which is this repo's own instructions rather than anything the plugin ships.)

| file | holds |
|---|---|
| `plugins/emotive/context/session-titles.md` | the glossary and every rule that is true in any repo; injected into every session by the `SessionStart` hook |
| `plugins/emotive/skills/emotive-setup/SKILL.md` | what an installing session does — where the instructions live, what to read from the repo, which skills to wire |
| `plugins/emotive/skills/emotive-setup/template.md` | the shape of the local half: what lands in one repo's own instructions, hazard paragraphs included |

The test for the first: would this sentence read the same in a repo you have never seen? If it
names a command, a branch or a resource, it belongs in `template.md` instead.

Match the existing style: one claim per paragraph, the reason on the same line as the rule, no
restructuring beyond what the change needs. `docs/glossary.md` has the wording rules for the
injected file — including why the glossary stays whole and never carries a placeholder.

Run `./build.sh` when the table changed — it re-pads the glossary in place and copies it into the
README. Report what landed as bullets.
