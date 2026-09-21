# Session-titles template

The text between the `BEGIN` and `END` markers is what gets written into the repo's agent
instructions. Everything outside them is guidance for writing it, and never gets installed.

Placeholders in `<angle brackets>` are filled from the repo. A paragraph that has nothing to say in
this repo is cut, not left generic — a section that describes a workflow the repo does not have is
worse than a shorter one.

<!-- BEGIN -->

## Session titles

A lifecycle prefix on the session title says what a session is doing while it is doing it, so the
sidebar answers "<the question this repo's parallel sessions actually raise — "which of these is
mid-ship", "which one is writing to the live database", "which chat holds the lock">" without
opening any of them. The sidebar already shows a status dot (running / awaiting input / idle) and a
branch glyph for worktree sessions; neither can be set from here — `set_session_title` takes a title
string and nothing else. So a **single leading emoji on the title** is the only lever, and it is
spent on what the app cannot know: where the work stands.

| Prefix | Means                                                                                      |
| ------ | ------------------------------------------------------------------------------------------ |
| `🎨 `  | brainstorming or designing with the user — exploring, sketching, deciding what to build    |
| `⏳ `  | implementing — the weakest of them; every other prefix takes precedence                    |
| `🔍 `  | auditing against live state — a dry run, or the plan it printed, with an apply to follow   |
| `🔓 `  | about to take that slot — queued or blocked on it — or just released it                   |
| `🔒 `  | holding the single slot <the repo can only run one of>                                      |
| `💾 `  | writing to <the live resource every session shares> right now                               |
| `📦 `  | done on the branch — <gated / tested> and shippable without re-running anything            |
| `🚀 `  | shipping to `<main branch>`, or shipped                                                     |
| `🚙 `  | parked: the work is sound and waiting on the user (<the things this repo waits on>)         |
| `⏲️ `  | waiting on a task scheduled for later — nothing to do until it fires                       |
| `🪦 `  | dead end — kept for the findings, not to resume                                             |
| `📚 `  | extracting learnings into <the repo's own learnings files>                                  |

The whole glossary ships, including rows for stages this repo has no way to reach yet. An unused row
is inert — it costs a line and settles the vocabulary before the workflow that needs it arrives,
rather than being invented under pressure by whichever session gets there first.

**Never mention a prefix in the response** — not what it was set to, not that it was already right,
not that it was left alone. It is sidebar state; say nothing about it unless asked.

These are **stages, not flags**: exactly one prefix at a time, and setting a new one replaces
whatever was there. **Every title carries one**, and a prefix comes off only when another takes its
place — a bare title says nothing about the session, and the sidebar cannot tell it apart from a
chat that never had a stage at all. A session with nothing left to do keeps the prefix of the last
stage it reached. The harness names a session, so every session starts without a prefix: putting the
first one on that inherited title is part of the first response, not something to wait for a stage
change to prompt. Only one reads cleanly at sidebar width, and `🚀 ` after `📦 ` is noise — the
later stage implies the earlier.

Set a prefix **optimistically** — when the stage _starts_, not when it succeeds — and correct it if
the stage falls over. A title that only becomes true at the end is blank for the whole stretch the
sidebar is there to describe. `🚀 ` is set by the `ship` skill, which sets it before the gates and
puts it back if the ship does not land, so it stays true on its own. `📚 ` goes on in the response
that starts extracting the session's learnings, before anything is read — whatever carries out the
extraction will not set a title itself. The rest are set in the response that enters the stage
(`mcp__ccd_session_mgmt__set_session_title`), and nothing reconciles a title against reality: an
abandoned session keeps whatever prefix it had.

**Handing back is itself a stage.** A response that closes on something for the user to do — <a
decision, a credential, a click> — is a park, and `🚙 ` goes on before that response, since the idle
dot cannot tell "waiting on you" from "given up on".

**A design loop is not a park.** `🎨 ` holds through brainstorming, and outranks `🚙 ` while it does:
the back-and-forth _is_ the stage, so a park prefix on every turn of it marks the whole session as
blocked and says nothing about what it is blocked on. It becomes `🚙 ` when the design is settled
and waiting on a decision to build it, and `⏳ ` when that decision comes. <A `brainstorm` or `plan`
skill sets it as its first step; otherwise it goes on in the response where the design loop starts.>

**Ask which session this is before renaming one.** `mcp__ccd_session_mgmt__get_session` with
`"self"` is the only answer, and it changes under a fork: a forked session carries the whole
transcript, the id it read earlier in that transcript, and a different id of its own, so a rename
that reuses the remembered one retitles the session it forked _from_. A fork also starts in the
worktree of the session it forked from, and nothing stops a branch being checked out there, which
moves that worktree under the other session's feet; put it back on the branch it was on when the
work is landed.

<A cloud session never reaches <the stages its environment rules out>: <why — credentials outside
git, a push convention the cloud harness refuses, hardware it does not have>. It ends at `🚙 `.>

<!-- END -->

## Paragraphs to add when the repo earns them

Every row above ships whether or not the repo can reach it; what is earned is the paragraph under
the table. A hazard prefix is worth more than the whole rest of the glossary, because it is the only
one another session acts on — and it is worth that only where the row names the repo's own commands
and a paragraph says what to do about them. A repo with no shared resource keeps the rows inert and
writes none of this.

| Prefix | For | The paragraph it needs |
| --- | --- | --- |
| `💾 ` | writing to a live resource every session shares — a production database, a real account, a deploy | Name the scripts that write, say that nothing locks it, and say to list sessions (`mcp__ccd_session_mgmt__list_sessions`) and look for another `💾 ` before touching it. A record that changes without your having changed it is that, not a bug. |
| `🔍 ` | diffing or auditing against live state, where an apply is about to follow | The stretch between a dry run and the apply it audits is stale the moment another session writes. Outranks a park, for the same reason `💾 ` does. |
| `🔒 `/`🔓 ` | a single slot the repo can only run one of — a dev-server port, a device, a browser profile | `🔓 ` covers queued, blocked and just-released; `🔒 ` means it is held right now and the slot serves this branch. Both are set by the skill that owns the lock, not in the response. |

Precedence: a hazard prefix (`💾 `, `🔍 `, `🔒 `) outranks a park, because the warning to other
sessions comes before the one to the user, who is already reading the response. `🚙 ` outranks
`⏲️ `, because a person can act and a clock cannot, and `🎨 ` outranks `🚙 ` for as long as the
design loop is live. `⏳ ` loses to everything.

Handing back while a hazard is still live keeps the hazard prefix, not `🚙 ` — the user clicking
through a console is still a write in flight.
