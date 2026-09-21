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
reaches everybody unresolved.

| file | holds |
|---|---|
| `plugins/emotive/context/session-titles.md` | the glossary and every rule that is true in any repo; injected into every session by the `SessionStart` hook |
| `plugins/emotive/skills/emotive-setup/SKILL.md` | what an installing session does — where the instructions live, what to read from the repo, which skills to wire |
| `plugins/emotive/skills/emotive-setup/template.md` | the shape of the local half: what lands in one repo's own instructions, hazard paragraphs included |

The test for the first: would this sentence read the same in a repo you have never seen? If it
names a command, a branch or a resource, it belongs in `template.md` instead. The injected file
ships as-is, and `./build.sh` fails on a `<placeholder>` in its glossary for that reason.

Match the existing style: one claim per paragraph, the reason on the same line as the rule, no
restructuring beyond what the change needs. Keep the glossary whole — a prefix a project cannot
reach is inert by design, not a candidate for removal.

Run `./build.sh` when the table changed — it re-pads the glossary in place and copies it into the
README. Report what landed as bullets.
