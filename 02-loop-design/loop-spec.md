# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> ✅ **What this validates:** the agent knows when to run and when to stop, by the end you'll have proven a one-page Loop Spec with a trigger, a definition of "done," and explicit stop conditions.
>
> Your one-page blueprint for how the work you handed to the agent (M1) actually *runs*.
> An agent is just a prompt that fires itself, this spec says when it fires, what "done" means, and what it needs to do the job. Living document; refine as the course progresses.

## 1. Trigger & loop type

**Chosen type:** cron (primary) + hook (backup)

- **Cron:** every Monday at 8:00, before the VP sync, Cortex assembles the weekly leadership update and proposes next sprint's stories. The update has a fixed weekly rhythm, so a schedule fits.
- **Hook:** when a Sev-1 opens on a project, Cortex drafts an alert immediately instead of waiting for Monday. Urgent risk can't wait a week.

**Ruled out:**
- **Heartbeat:** constant polling burns cost for a weekly job; the hook already covers urgent events.
- **Goal:** the work has a set rhythm and a clear deliverable, not an open-ended goal.

**Idempotency:** dedupe by project + ISO week for the cron, and by issue ID for the hook, so a double-fire never drafts two updates or two alerts.

## 2. Goal / definition of done

A weekly leadership status update for one project, grounded only in pulled data (project, activity, past updates, norms), that passes the independent critic and is queued for my review together with the proposed stories (max 10). Cortex never posts, sends, or commits anything. Done is validated by the critic subagent, not by Cortex grading itself.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | Critic verdict = `pass` and the draft + stories are in the review queue | Stop. Notify me that it's ready for review |
| **Stuck / give up** | Critic rejects 2× (revision cap) · 8 iterations without finishing · run cost ≥ $0.50 · a tool returns no data / project not found | Halt, log the reason + last draft, hold it, nothing posted |
| **Escalate to human** | Open Sev-1 or `launch_hold` flag · embargoed/confidential project in context (e.g. Orbit) · draft needs a date commitment · story batch over the cap of 10 | HITL checkpoint from the M1 agent line: hand to me, I decide |

## 4. State

Scoped **per project, per run**. Across iterations Cortex carries: the pulled data, the current draft, the critic's feedback, the revision count, the iteration count, and the running cost. Across runs, only last week's update for that same project persists. Nothing carries across projects, so confidential context (e.g. embargoed Orbit) can never leak into another project's update.

## 5. The five things a loop can lean on

_`state` is always-on. `connectors` only if you already have one wired (e.g. a Jira key or Google MCP), otherwise just note it as a plan. `skills`, `subagents`, `work tree` scale with autonomy; "not needed yet, because…" is a valid answer._

| Component | For Cortex |
|---|---|
| **Work tree** (isolated workspace per run, a git worktree) | Not needed yet, because one project runs at a time and Cortex writes nothing to the repo, only a held draft. Needed once several projects run in parallel. |
| **Skills** (reusable capabilities) | Not needed yet, because there's one job (the weekly update). Later: a reusable "status update" skill with the format + Green/Yellow/Red rules, shared across projects. |
| **Plugins / connectors** (tools & access, optional if you don't have one yet) | Plan only: reads local `fixtures/` today. Next: read-only Jira + GitHub connectors. No write or post access, so the agent line holds. |
| **Subagents** (independent check when the loop can't grade itself) | _placeholder → M3 orchestration-map.md_ (today: the critic already acts as an independent checker) |
| **State tracking** | Revision count (cap 2), iteration count (cap 8), running cost (cap $0.50), per-project dedupe key (project + week). |

> Context plan (M4) and the hand-off to bounds & evals (M5) come in later modules, you'll add them to their own deliverables then, not here.

## Link to live loop

[`00-build/agent.py`](../00-build/agent.py), with the definition of done and stop conditions in [`00-build/prompts.py`](../00-build/prompts.py). Verified 2026-11-07: the happy-path run hit the **Success** exit (critic passed, queued for review, nothing posted, ~$0.002).
