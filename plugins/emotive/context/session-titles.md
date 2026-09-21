## Session titles

A lifecycle prefix on the session title says what a session is doing while it is doing it, so the
sidebar answers "which of these is mid-ship" — or mid-write, or waiting on me — without opening any
of them. The sidebar already shows a status dot (running / awaiting input / idle) and a branch glyph
for worktree sessions; neither can be set from here — `set_session_title` takes a title string and
nothing else. So a **single leading emoji on the title** is the only lever, and it is spent on what
the app cannot know: where the work stands.

| Prefix | Means                                                                                    |
| ------ | ---------------------------------------------------------------------------------------- |
| `🎨 `  | brainstorming or designing with the user — exploring, sketching, deciding what to build  |
| `⏳ `  | implementing — the weakest of them; every other prefix takes precedence                  |
| `🔍 `  | auditing against live state — a dry run, or the plan it printed, with an apply to follow |
| `🔓 `  | about to take that slot — queued or blocked on it — or just released it                  |
| `🔒 `  | holding the single slot only one session can use at a time                               |
| `💾 `  | writing to a live resource every session shares right now                                |
| `📦 `  | done on the branch — gated and shippable without re-running anything                     |
| `🚀 `  | shipping to the default branch, or shipped                                               |
| `🚙 `  | parked: the work is sound and waiting on the user (a decision, a credential, a click)    |
| `⏲️ `  | waiting on a task scheduled for later — nothing to do until it fires                     |
| `🪦 `  | dead end — kept for the findings, not to resume                                          |
| `📚 `  | extracting learnings into the instruction files                                          |

The whole glossary applies in every project, including rows for stages a project has no way to
reach. An unused row is inert — it costs nothing and settles the vocabulary before the workflow that
needs it arrives, rather than leaving it to be invented under pressure by whichever session gets
there first.

A project's own instructions may say what its rows mean *here* — which branch `🚀 ` ships to, what
gate makes a branch `📦 `, which live resource `💾 ` is warning about, which stages a cloud session
cannot reach. That names the local half; it never replaces a row or drops one.

**Never mention a prefix in the response** — not what it was set to, not that it was already right,
not that it was left alone. It is sidebar state; say nothing about it unless asked.

These are **stages, not flags**: exactly one prefix at a time, and setting a new one replaces
whatever was there — only one reads cleanly at sidebar width, and `🚀 ` after `📦 ` is noise, since
the later stage implies the earlier. **Every title carries one**, and a prefix comes off only when
another takes its place: a bare title says nothing about the session, and the sidebar cannot tell it
apart from a chat that never had a stage at all. A session with nothing left to do keeps the prefix
of the last stage it reached. The harness names a session, so every session starts without a prefix:
putting the first one on that inherited title is part of the first response, not something to wait
for a stage change to prompt.

Set a prefix **optimistically** — when the stage _starts_, not when it succeeds — and correct it if
the stage falls over. A title that only becomes true at the end is blank for the whole stretch the
sidebar is there to describe. A `ship` skill sets `🚀 ` before its gates and puts back what is true
if the ship does not land, so that one stays honest on its own. `📚 ` goes on in the response that
invokes a `learn` skill, before anything is read — the skill does not set it itself. The rest are
set in the response that enters the stage (`mcp__ccd_session_mgmt__set_session_title`), and nothing
reconciles a title against reality: an abandoned session keeps whatever prefix it had.

**Handing back is itself a stage.** A response that closes on something for the user to do — a
decision, a credential, a click — is a park, and `🚙 ` goes on before that response, since the idle
dot cannot tell "waiting on you" from "given up on".

**A design loop is not a park.** `🎨 ` holds through brainstorming, and outranks `🚙 ` while it does:
the back-and-forth _is_ the stage, so a park prefix on every turn of it marks the whole session as
blocked and says nothing about what it is blocked on. It becomes `🚙 ` when the design is settled and
waiting on a decision to build it, and `⏳ ` when that decision comes.

Precedence, when two could apply: a hazard (`💾 `, `🔍 `, `🔒 `) outranks a park, because the warning
to other sessions comes before the one to the user, who is already reading the response — handing
back while a write is still in flight keeps the hazard prefix. `🚙 ` outranks `⏲️ `, because a person
can act and a clock cannot. `⏳ ` loses to everything.

**Ask which session this is before renaming one.** `mcp__ccd_session_mgmt__get_session` with
`"self"` is the only answer, and it changes under a fork: a forked session carries the whole
transcript, the id it read earlier in that transcript, and a different id of its own, so a rename
that reuses the remembered one retitles the session it forked _from_. A fork also starts in the
worktree of the session it forked from, and nothing stops a branch being checked out there, which
moves that worktree under the other session's feet; put it back on the branch it was on once the
work has landed.

Where `set_session_title` is not among the available tools there is no title to set, and none of
this applies — it is the desktop app's, and a terminal session has no sidebar to answer to.
