---
name: ship
description: Commit and push all changes, release the plugin, then extract the session's learnings. Use when the user says "ship" or invokes /ship.
---

# Ship

Run the whole sequence unattended. Never stop and ask the user to merge, tag, or update anything themselves.

1. Set the session title prefix to 🚀 (see Session titles in AGENTS.md). Say nothing about it.
2. `./build.sh` — it fails on a placeholder with no generic wording, which is a real failure, not a
   formatting nit. Fix it rather than hand-editing the README block.
3. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json`.
4. Commit all changes.
5. `git fetch origin && git rebase origin/main` — resolve any conflicts.
6. `git push` (add `--set-upstream origin <branch>` on the first push of a branch).
7. Land it on `main`. The plugin marketplace serves `main`, so a release left on a
   branch has not shipped.
   - Locally: `git push origin HEAD:main`.
   - From a cloud session that is refused: open a PR for the branch and merge it
     with the GitHub tools. Rebase-merge, to keep `main` linear.
8. Confirm the tag. `.github/workflows/tag-release.yml` tags `v<version>` when the
   version bump lands on `main`; never tag by hand.
   `git fetch origin --tags --force && git tag -l v<version>` — retry for up to a
   minute while Actions runs.
9. `claude plugin marketplace update emotive && claude plugin update emotive`
   — without the marketplace refresh, the cached listing reports the pre-push version as latest.
   Skip this where the plugin is not installed: a machine running the skill through the
   `~/.claude/skills/emotive-setup` symlink already has the new files, and the commands report
   an unknown plugin. Say which of the two it was.
10. Invoke the `learn` skill. A shipped release is the moment its lessons are worth
    writing down, and it needs no ask — it is the last stage of shipping. Skip it only
    when `learn` is what invoked this ship (its own procedure ends in one), or the two
    would call each other forever. `learn` puts `📚 ` on the title; put `🚀 ` back when
    it finishes. If it finds nothing worth recording, say so in one line.
11. Print `🚀 Shipped`

If step 8 finds no tag, say which version is untagged; do not report a clean ship.
If the push fails, put the prefix back to what it was.

A change to what a prefix *means* is not finished by this ship: the sibling repos carry field copies
of the glossary (see AGENTS.md → The vocabulary is shared, not invented here). Say which repos still
carry the old wording rather than reporting the change as landed everywhere.

Report the commit subject, the new version, and whether the plugin updated. Skip narration.
