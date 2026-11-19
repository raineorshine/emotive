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

The column is padded to its longest cell, so a new row longer than the rest
reflows every row in both copies. Word it to fit the existing width where it can,
and the diff stays one line.

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

A session whose shape no row plainly names sets no prefix at all, rather than the
nearest one: a chat that only found an issue and answered stayed bare while `🧹 ` read as
organizing sessions alone. When a common session shape goes unprefixed, widen the row nearest
it or add one — the "every title carries one" rule cannot cover a gap in the table.

A prefix a project cannot reach is inert by design, not a candidate for removal.
An unused row costs a session nothing, and settles the vocabulary before the workflow
that needs it arrives — but every row spends part of a fixed budget: the injected file
has to stay under the hook's inline limit ([hook-injection.md](hook-injection.md)), so a
row, or a paragraph for it, is paid for by tightening wording elsewhere.

A new row reaches past the table: the row count `emotive-setup/SKILL.md` states,
the prefix character class in `docs/sweep.md`'s row grep, and every paragraph
listing the prefixes a session rests at or ranks against. Grep for the count and
for a neighbouring row's emoji before calling the row added.

## An exception amends every absolute it breaks

The glossary states its rules as absolutes — "never what a session rests at", "the
one place", "loses to everything" — and each is repeated where a neighbouring
prefix leans on it. Letting `⏳ ` rest while a background run of Claude works took
three edits: the rule itself, the `✈️ ` paragraph that called itself the only
exception, and the precedence paragraph. Grep the file for the prefix and for
`Precedence` before adding an exception, and amend every absolute it contradicts in
the same change, or the injected copy argues with itself.

The boundary of that exception — a run of Claude versus a server left up for the
user — is also encoded in supernova, which keeps a chat's card working under a stale
`🚙 ` on the same test. A change to where that line falls has to be made in both.

Supernova also draws `🧹 ` itself — as the tasklist octicon on a card's working chip, in
`STAGE_ICONS` (`components/stageIcons.tsx`) — so changing that prefix's character is an edit there
too.
