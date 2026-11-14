# Context Engineering & Memory: Cortex PM Chief-of-Staff Agent

> Module 4 · Context Engineering & Memory
>
> ✅ **What this validates:** the agent reasons on the right, safe inputs, by the end you'll have proven a context budget, per-source retrieve-vs-long-context decisions, and a memory map with risk mitigations.
>
> 🗂️ **How the lab maps to this file:** In **Part A** (before the lecture) you don't edit this file, you rough-draft on scratch, focused on the per-source calls in **section 2** plus a quick remember/forget + "how it rots" sketch. In **Part B** (after the lecture) you complete **all five sections**; the Lab Guide's guided builder writes this file for you to copy in and commit.

## 1. Context budget

Each run, Cortex gets only what this one update needs, for **one project**. When space is tight, this is the priority order:

1. **Task brief**: the job itself.
2. **Team norms**: the rules every draft must follow.
3. **Project record**: status, flags, sprint, PRD summary.
4. **This week's activity slice**: merged PRs, open issues, metrics.
5. **Last 2–3 past updates for this project**: tone and precedent.
6. **Roadmap slice**: this project's shareable section only.

Everything else (other projects, older history, confidential roadmap items) stays out of the window.

## 2. Retrieve vs. long-context: per source

For each data source, decide: **retrieve** (narrow a large/changing corpus to the relevant slice) or **long-context** (just include a bounded set you can reason over).

| Source | Size / volatility | Decision | Why |
|---|---|---|---|
| Task brief (`get_task`) | Tiny, one doc · new every run | **Long-context** | Deciding factor: **size**. One bounded doc; Cortex needs all of it. |
| Project record (`get_project`) | Small, one record · weekly | **Long-context** (this project only) | Deciding factor: **size**. One record; cheap to include whole. |
| GitHub/Jira activity (`get_activity`) | Large, grows daily | **Retrieve** | Deciding factor: **volatility + citation**. Only this project's latest slice, and every PR/issue ID must be citable. |
| Past updates + decision log (`search_past_updates`) | Unbounded · grows weekly | **Retrieve** | Deciding factor: **size**. Only this project's last 2–3 updates and related decisions. |
| Roadmap (`get_roadmap`) | Medium · monthly | **Retrieve** | Deciding factor: **citation / safety**. Only this project's shareable section; CONFIDENTIAL items (Orbit, Pulsar) never enter the window. |
| Team norms (`get_norms`) | Medium, bounded · rarely changes | **Long-context** (flipped from my Part A call) | Deciding factor: **size**. Small enough to include whole, and every rule must apply; a retrieved slice could miss the one rule that mattered (e.g. Sev-1 → not Green). |

## 3. Retrieval quality plan

_Which of these apply, and how? (This is what separates modern agentic retrieval from naive "embed → top-k → stuff".)_

Only the three **retrieved** sources need these moves; each move is picked for how that source fails.

| Source | How it could fail | Routing | Grading | Reranking | Self-verification | Caching |
|---|---|---|---|---|---|---|
| **Activity** | Wrong project, or stale activity | ✅ by `project_id` | ✅ this project, this week only | · | ✅ critic: every cited PR/issue ID must exist in the pulled data | ✗ changes daily, always fetch fresh |
| **Past updates + decisions** | Copies a stale or another project's update | ✅ this project only | ✅ drop other projects | ✅ newest first, keep last 2–3 | · | · |
| **Roadmap** | Leaks a confidential item (Orbit, Pulsar) | ✅ this project's section | ✅ drop anything marked CONFIDENTIAL | · | ✅ critic leak check | ✅ changes monthly |

- **Routing**: send each question to the right tool and project, never search everything.
- **Document grading**: check what came back is relevant (and safe) before it enters the window.
- **Reranking**: put the most useful items first; for past updates, that's the newest.
- **Self-verification**: the M3 critic checks that every claim in the draft traces to retrieved evidence.
- **Caching**: reuse the slow-changing roadmap within the month; never cache activity.

## 4. Memory map (your PM brain)

| Memory type | What Cortex stores | Scope / TTL |
|---|---|---|
| **Working** (in-loop) | This run's pulled data, drafts, critic feedback, revision/iteration/cost counters | This run only, wiped after |
| **Episodic** (past runs) | **PM-approved** past updates and escalations for this project (e.g. "escalated the GA date") | Per project, 4 weeks, then ages out |
| **Semantic** (durable facts/prefs) | Team norms, project → PRD mapping, the embargoed list (Orbit, Pulsar) | Long-lived, reviewed quarterly; changed only by a human |
| **Shared** (across agents) | Cortex → Critic hand-off: draft + source data; verdict + reasons back | This run only |

## 5. Memory risks & mitigations

| Risk | Where it bites Cortex | Mitigation |
|---|---|---|
| **Drift** | Norms or the embargoed list change, but Cortex's stored copy doesn't | Re-read norms every run (long-context, §2); human-owned quarterly review of semantic memory |
| **Poisoning** | A bad draft or jailbreak text gets saved to memory and trusted on later runs | Only PM-approved updates are written to episodic memory; writing to memory sits **above the agent line** (M1) |
| **Staleness** | Remembers "Northstar = Green" and misses this week's Sev-1 | Never trust memory for live facts: status and activity are re-pulled every run; episodic memory expires after 4 weeks |
| **Confidential / retention** | Embargoed roadmap (Orbit, Pulsar) or sensitive info lingers in memory | Store the minimum, scope per project, never store CONFIDENTIAL content; TTLs enforced as **M5 bounds** |
