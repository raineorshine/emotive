# Sweeping a sibling repo

Cutting the pre-hook glossary copy out of a repo that already adopted the
convention, and reconciling its skills with the current rows. Read this before
running `emotive-setup` against a repo that is not this one.

Six repos were swept this way — `regard`, `github-triage`, `a-thousand-worlds`,
`axshot`, then `karabiner` and `email-filter-builder` — which is the sample
everything below comes from.

## Never run the target's own `ship` skill

`emotive-setup`'s landing step says to follow the repo's own ship skill. For a
sweep, do not. A sweep is a docs-only change, and a sibling repo's gates are not
docs-only: `regard`'s and `axshot`'s install the built app into `/Applications`
and drive the user's real keyboard, behind a lock. Running that unattended, for a
change that touches one markdown section, takes over the machine.

Commit the edit directly instead, and say in the sweep's own report that the gate
was skipped and why.

## Find the copy before concluding there isn't one

The section is not always `## Session titles` in `AGENTS.md`. `email-filter-builder`
has it as a `###` under `## Repo`, and `karabiner` keeps it in `docs/workflow.md`
with `AGENTS.md` not mentioning it at all. A `grep -c '^## Session titles'` reported
both as unswept-and-empty when both held the full glossary.

Grep for a row instead of a heading, and allow the prefix cell either spelling —
some repos write `` | `🚀 ` | `` and some write `| 🚀 |`:

```bash
grep -rn '^| `\?[🎨⏳🔍🔓🔒💾📦🚀🚙⏲🪦📚]' --include='*.md' .
```

## Read the target's git state before touching it

Every one of the four had unpushed commits on `main`, and one had diverged
(ahead 3, behind 2). Neither is the sweep's business:

- **Unpushed commits** ride along on any push. Read them first. If they are
  unrelated work, commit locally and leave the push to whoever owns them.
- **A diverged `main`** needs a rebase whose conflicts sit in work the sweep has
  not read. Resolve it only if asked, and expect a conflict where the remote
  edited near a heading the sweep renamed — keeping both sides is usually right,
  since the two changes are about different things.
- A rebase may drop local commits that reached the remote by another route. Three
  local commits becoming one is a normal outcome, not a loss.

## Parallel, but only without `--ask`

Sibling repos are independent, so sweeps run at once with nothing to coordinate —
the opposite of two sessions shipping the same repo, where the version number
collides (see AGENTS.md → Shipping while another session is shipping). One
background session per repo, and four at once was fine.

**`--ask` cannot be backgrounded at all.** The dialog has to reach the user, and a
headless session has nobody to answer it, so an asking sweep runs from the session
the user is talking to — serially, one repo at a time. `AskUserQuestion` caps a
call at four questions and the ask is three, so it is one dialog per repo rather
than one for the batch.

Driving it from elsewhere means the skill's step 0 retitles the *driving* session,
not the target. That prefix is the driving session's own stage; it is not a preview
of what the target chose.

## The hazard paragraph is what a sweep is most likely to destroy

The sweep is a subtraction, and the rows are the easy part. What is at risk is the
paragraph naming the repo's own commands — `a-thousand-worlds` has five, about
`npm install` and `npm run update:dbcache` writing through the `node_modules`
symlink, the dev server snapshotting `public/` at startup, hot reload not reaching
the Browser pane, and which reads are *not* `💾 ` work. Those look like prose to
cut and are the most valuable lines in the file.

Name them in the sweep's prompt so they survive, and check they did afterwards.

## Check the result, not the report

Per repo, after it finishes: the row grep above returns nothing, the hazard
paragraphs are still present, the `ship` skill sets `🚀 ` after its landing rather
than at step 0, and no link points at a section the sweep deleted — `regard` had a
`docs/workflow.md#session-titles` anchor pointing at one.

A repo may also come out needing a skill that never existed. `email-filter-builder`
had no owner for its `🔍 ` → `💾 ` pair: `src/sync.js` is a dry run by default and
`--apply` writes, so the two prefixes bracketed one operation and were being set
from memory in a response. That is the same argument as the `🚀 `-row-with-no-owner
bug, and the fix is the same — write the skill.
