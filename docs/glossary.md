# Editing the glossary

The wording rules for `plugins/emotive/context/session-titles.md`, and what
`./build.sh` does to its table. Read this before changing a row or adding one.

## Never hand-pad a table

An emoji is two columns wide, and a variation selector is zero but widens the
character before it, so a table padded by character count comes out ragged.
`build.sh` pads the glossary by display width in both places it appears, so
neither is ever typed; a table written by hand elsewhere needs the same
arithmetic, or prettier's.

The README shows a prefix as a bare emoji — 📚, never `📚 `; the backticks and the
trailing space belong to `context/session-titles.md`, where a session reading it is
about to set a title. `build.sh` keeps two row lists for that, parsed and stripped,
padded separately — a check wanting the source's own spelling must read the
unmutated one, which an earlier coverage check got wrong and matched nothing.

The build fails on a `<placeholder>` in the glossary, because that file ships
as-is: a placeholder there would reach every session unresolved. Tailored wording
belongs in `template.md`.

## Name a stage by what happens in it

Never by a skill only this machine has. The `📚 ` row said "the response that
invokes `learn`", and `learn` is a user-level skill nobody else has — so the
injected copy names the pass instead, and reads the same in any project.

Say a prefix is **set in the response that enters the stage** — never "by hand",
which reads as something the user does when every setter is an agent. The
distinction the wording has to carry is whether a skill owns the stage and
re-reads it each run, or a response sets it inline from an instruction it has to
remember.

## Keep the glossary whole

A prefix a project cannot reach is inert by design, not a candidate for removal.
Adding a row costs nothing anywhere for the same reason, and an unused row settles
the vocabulary before the workflow that needs it arrives.
