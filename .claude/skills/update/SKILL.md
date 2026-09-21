---
name: update
description: Update the setup-emoji-status skill per a given prompt. Use when the user invokes /update <prompt>.
argument-hint: <prompt>
---

# Update

Set the session title prefix to ⏳ before editing (see Session titles in AGENTS.md). Say nothing about it.

Apply this instruction to the skill:

$ARGUMENTS

Put it in the right half. `SKILL.md` holds what an installing session does — where the instructions
live, what to read from the repo, which skills to wire. `template.md` holds what the installed
project reads afterwards: the section between its `BEGIN`/`END` markers, and the guidance around it
for the parts that are tailored. A rule in the wrong file either never runs or ships into every
project that adopts the convention.

Match the existing style: one claim per paragraph, the reason on the same line as the rule, no
restructuring beyond what the change needs. Keep the glossary whole — a prefix a project cannot
reach is inert by design, not a candidate for removal.

Run `./build.sh` when the table changed. Report what landed as bullets.
