# The local half

The glossary ships in `../../context/session-titles.md` and is injected into every session by the
plugin's `SessionStart` hook. Nothing in this file repeats it. What this file shapes is the section
a project keeps in its own instructions: the handful of things the injected copy states generically
and this repo can state exactly.

The text between the `BEGIN` and `END` markers is the shape, not the content. Placeholders in
`<angle brackets>` are filled from the repo; **a line with nothing to fill it is cut, not left
generic.** A section that restates what the hook already said is worse than no section at all — it
is the duplicate the hook exists to eliminate, reintroduced by hand.

Most repos end up with three or four lines here. A repo with a shared resource ends up with a
paragraph as well, and that paragraph is worth more than everything else on this page.

<!-- BEGIN -->

## Session titles

The prefix glossary arrives from the `emotive` plugin; these are the parts that are specific to this
repo.

- `📦 ` means <the gate — `npm test && npm run lint`, `./build.sh` clean, or "done on the branch",
  where there is no gate to run>.
- `🚀 ` ships to `<branch>`, <through a PR / by fast-forward>; `<the ship skill>` is that procedure
  and sets the prefix itself, once the push lands. <`📦 ` holds until then.>
- `🚙 ` is what this repo waits on a user for: <a credential, an OAuth click, a look at the branch>.
- `💾 ` / `🔒 ` <name the shared thing, or say they are inert here and why>.

<the hazard paragraph, where there is a hazard — see below>

<A cloud session never reaches <the stages its environment rules out>: <why — credentials outside
git, a push convention the cloud harness refuses, hardware it does not have>. It ends at `🚙 `.>

<!-- END -->

## The hazard paragraph

A hazard prefix is the only one another session acts on, and it is worth that only where a paragraph
says what to do about it. This is the part of the convention that cannot be injected, because it is
made of this repo's own commands.

| Prefix | For | The paragraph it needs |
| --- | --- | --- |
| `💾 ` | writing to a live resource every session shares — a production database, a real account, a deploy | Name the scripts that write, say that nothing locks it, and say to list sessions (`mcp__ccd_session_mgmt__list_sessions`) and look for another `💾 ` before touching it. A record that changes without your having changed it is that, not a bug. Say what is *not* `💾 ` work too — a read through the same path is not a write. |
| `🔍 ` | diffing or auditing against live state, where an apply is about to follow | The stretch between a dry run and the apply it audits is stale the moment another session writes. Name the dry-run command and the apply it pairs with. |
| `🔒 `/`🔓 ` | a single slot the repo can only run one of — a dev-server port, a device, a browser profile | `🔓 ` covers queued, blocked and just-released; `🔒 ` means it is held right now and the slot serves this branch. Both are set by the skill that owns the lock, not in the response — name that skill. |

A worktree layout has hazards a single checkout does not: a symlinked `node_modules` that an
install writes through, a shared `.env`, a dev server that snapshots a directory at startup. Those
belong here, named by command, because no injected copy can guess them.

## Saying that a row is inert

A repo with nothing shared still says so in one line — `💾 `, `🔍 ` and `🔒 ` are inert here,
nothing is shared across sessions — rather than leaving them unmentioned. The rows arrive in every
session whether or not the repo can reach them; one line tells the next session that the silence was
a decision and not an oversight.
