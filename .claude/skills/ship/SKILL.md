---
name: ship
description: Commit and push all changes, release the plugin, then extract the session's learnings. Use when the user says "ship" or invokes /ship.
---

# Ship

Run the whole sequence unattended. Never stop and ask the user to merge, tag, or update anything themselves.

0. Leave the title's prefix alone for now. `🚀 ` means shipped, and nothing is shipped until step 6
   lands — a title that says so earlier is wrong for the whole ship, and stays wrong if the ship
   falls over. A shipping session keeps whatever is true meanwhile, usually `📦 ` (see Session titles
   in AGENTS.md).
1. `./build.sh` — it fails on a placeholder with no generic wording, which is a real failure, not a
   formatting nit. Fix it rather than hand-editing the README block.
2. Commit all changes.
3. `git fetch origin && git rebase origin/main` — resolve any conflicts, then run
   `./build.sh` again. A clean rebase still moves the files the build parses, so a
   check this branch added can meet a format `main` changed under it.
4. Bump the minor version in `plugins/emotive/.claude-plugin/plugin.json` — unless the
   ship touches nothing under `plugins/emotive/`, in which case skip this step and
   steps 7 and 8, and say the ship carried no release. An install delivers that
   directory alone, so a bump there would announce an update that changes nothing
   installed. Bump **after**
   the rebase and off `git show origin/main:plugins/emotive/.claude-plugin/plugin.json`
   — not off the working tree, which an abandoned earlier attempt may already have
   bumped, giving a double increment. Then commit the bump. Bumping
   before means bumping off a stale `main`: another session shipping meanwhile takes the
   number, and its tag is already pushed, so the release has nowhere to land. Derive it,
   never remember it.
5. `git push` (add `--set-upstream origin <branch>` on the first push of a branch).
6. Land it on `main`. The plugin marketplace serves `main`, so a release left on a
   branch has not shipped. Re-check `git log HEAD..origin/main` immediately before the
   push: the window between resolving a rebase and pushing is exactly when another
   session lands something, and a rejected `HEAD:main` push means going back to step 3
   rather than forcing it. Never force-push `main`.
   - Locally: `git push origin HEAD:main`.
   - From a cloud session that is refused: open a PR for the branch and merge it
     with the GitHub tools. Rebase-merge, to keep `main` linear.
   **Once the push succeeds, and not before,** read the title
   (`mcp__ccd_session_mgmt__get_session` with `"self"`) and set it back with a `🚀 ` prefix,
   replacing the existing one rather than stacking. Say nothing about it.
7. Confirm the tag. `.github/workflows/tag-release.yml` tags `v<version>` when the
   version bump lands on `main`; never tag by hand.
   `git fetch origin --tags --force && git tag -l v<version>` — retry for up to a
   minute while Actions runs.
8. `claude plugin marketplace update emotive && claude plugin update emotive`
   — without the marketplace refresh, the cached listing reports the pre-push version as latest.
   Skip this where the plugin is not installed, and say so — but flag it rather than passing
   over it: the `SessionStart` hook that carries the glossary only runs for an installed
   plugin, so a machine with the `~/.claude/skills/emotive-setup` symlink and no plugin has
   the new skill and none of the new glossary.
9. Add the session's learnings to `AGENTS.md`, the README and the skills. A shipped
   release is the moment its lessons are worth writing down, and it needs no ask — it is
   the last stage of shipping. Skip it only when a learning pass is what invoked this
   ship (one ends in a ship of its own), or the two would call each other forever. Put
   `📚 ` on the title as you start — nothing else sets it — and `🚀 ` back when it
   finishes; say nothing about either. If there is nothing worth recording, say so in
   one line.
10. Print `🚀 Shipped`

If step 7 finds no tag, say which version is untagged; do not report a clean ship.

The `🚀 ` set in step 6 stays through the report and after it, until the session starts something
else — never cleared to leave a bare title. If the ship never got that far, no `🚀 ` went on and
there is nothing to undo; check the title still says what is true now (`📦 ` for a gated branch,
`⏳ ` if the work goes back to implementing, `🚙 ` if it waits on the user) and correct it if not.
Say nothing about it either way.

A change to what a prefix *means* is not finished by this ship: the sibling repos carry field copies
of the glossary (see AGENTS.md → The vocabulary is shared, not invented here). Say which repos still
carry the old wording rather than reporting the change as landed everywhere.

Report the commit subject, the new version, and whether the plugin updated. Skip narration.
