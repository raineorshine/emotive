---
name: ship-at-end
description: Implement the request, then run /ship if all looks good, asking design or clarifying questions up front. Use when the user invokes /ship-at-end.
disable-model-invocation: true
---

Implement and then /ship if all looks good. Ask designs questions or questions for clarification up
front if you have any.

This is an autopilot: put `✈️ ` on the session title in the response that starts implementing —
the first one, unless it asks questions first — replacing any prefix already there. `✈️ ` holds
through the implementation and the ship until `/ship` sets `🚀 `, and never rests at `📦 `. If
something goes wrong and the work needs the user, set `🚙 ` and say what is blocking.
