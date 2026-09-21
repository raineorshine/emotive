# Sweeping a sibling repo

Cutting the pre-hook glossary copy out of a repo that already adopted the
convention, and reconciling its skills with the current rows. Read this before
running `emotive-setup` against a repo that is not this one.

Four repos were swept this way in one pass — `regard`, `github-triage`,
`a-thousand-worlds`, `axshot` — which is the sample everything below comes from.

## Never run the target's own `ship` skill

`emotive-setup`'s landing step says to follow the repo's own ship skill. For a
sweep, do not. A sweep is a docs-only change, and a sibling repo's gates are not
docs-only: `regard`'s and `axshot`'s install the built app into `/Applications`
and drive the user's real keyboard, behind a lock. Running that unattended, for a
change that touches one markdown section, takes over the machine.

Commit the edit directly instead, and say in the sweep's own report that the gate
was skipped and why.

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

## Parallel is safe here, unlike concurrent ships

Sibling repos are independent, so the sweeps run at once with nothing to
coordinate — which is the opposite of two sessions shipping the same repo, where
the version number collides (see AGENTS.md → Shipping while another session is
shipping). One background session per repo.

## The hazard paragraph is what a sweep is most likely to destroy

The sweep is a subtraction, and the rows are the easy part. What is at risk is the
paragraph naming the repo's own commands — `a-thousand-worlds` has five, about
`npm install` and `npm run update:dbcache` writing through the `node_modules`
symlink, the dev server snapshotting `public/` at startup, hot reload not reaching
the Browser pane, and which reads are *not* `💾 ` work. Those look like prose to
cut and are the most valuable lines in the file.

Name them in the sweep's prompt so they survive, and check they did afterwards.

## Check the result, not the report

Per repo, after it finishes: no glossary rows left in the table
(`grep -c '^| \`[🎨⏳🔍🔓🔒💾📦🚀🚙⏲🪦📚]' AGENTS.md` returns 0), the hazard
paragraphs still present, the `ship` skill setting `🚀 ` after its landing rather
than at step 0, and no dangling links to a section the sweep deleted — `regard`
had a `docs/workflow.md#session-titles` anchor pointing at one.
