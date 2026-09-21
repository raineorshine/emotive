---
name: ship
description: Commit and push all changes, release the plugin, then extract the session's learnings. Use when the user says "ship" or invokes /ship.
---

# Ship

Run the whole sequence unattended. Never stop and ask the user to merge, tag, or update anything themselves.

0. Set the session title prefix to 🚀 , replacing whatever prefix is there rather than stacking on
   it — before the build, not after it lands (see Session titles in AGENTS.md). Say nothing about it.
1. `./build.sh` — it fails on a placeholder with no generic wording, which is a real failure, not a
   formatting nit. Fix it rather than hand-editing the README block.
2. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json`.
3. Commit all changes.
4. `git fetch origin && git rebase origin/main` — resolve any conflicts.
5. `git push` (add `--set-upstream origin <branch>` on the first push of a branch).
6. Land it on `main`. The plugin marketplace serves `main`, so a release left on a
   branch has not shipped.
   - Locally: `git push origin HEAD:main`.
   - From a cloud session that is refused: open a PR for the branch and merge it
     with the GitHub tools. Rebase-merge, to keep `main` linear.
7. Confirm the tag. `.github/workflows/tag-release.yml` tags `v<version>` when the
   version bump lands on `main`; never tag by hand.
   `git fetch origin --tags --force && git tag -l v<version>` — retry for up to a
   minute while Actions runs.
8. `claude plugin marketplace update emotive && claude plugin update emotive`
   — without the marketplace refresh, the cached listing reports the pre-push version as latest.
   Skip this where the plugin is not installed: a machine running the skill through the
   `~/.claude/skills/emotive-setup` symlink already has the new files, and the commands report
   an unknown plugin. Say which of the two it was.
9. Invoke the `learn` skill. A shipped release is the moment its lessons are worth
   writing down, and it needs no ask — it is the last stage of shipping. Skip it only
   when `learn` is what invoked this ship (its own procedure ends in one), or the two
   would call each other forever. Put `📚 ` on the title as you invoke it — the
   user-level `learn` does not set one itself — and `🚀 ` back when it finishes; say
   nothing about either. If it finds nothing worth recording, say so in one line.
10. Print `🚀 Shipped`

If step 7 finds no tag, say which version is untagged; do not report a clean ship.
If the ship does not land, put the title back to what is true now: `📦 ` if the branch built clean
but stayed on the branch, `⏳ ` if the work goes back to implementing, `🚙 ` if it is waiting on the
user. If it did land, `🚀 ` stays — never clear it to a bare title. Say nothing about it either way.

A change to what a prefix *means* is not finished by this ship: the sibling repos carry field copies
of the glossary (see AGENTS.md → The vocabulary is shared, not invented here). Say which repos still
carry the old wording rather than reporting the change as landed everywhere.

Report the commit subject, the new version, and whether the plugin updated. Skip narration.
