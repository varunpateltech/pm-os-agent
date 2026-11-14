# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> ✅ **What this validates:** nothing advances unchecked, by the end you'll have proven a justified topology, a roster, and a validator with a defined fail action.
>
> Builds on your M2 Loop Spec. Only split one agent into a team when there's a real reason, coordination has a cost.

## 1. Why split? (or why not)

Cortex is one agent that pulls project data, drafts the weekly update, and proposes next sprint's stories. I split it only once: an **independent validator** (the critic). Cortex can't grade its own draft; in my M2 runs the critic caught a wrong status call and a placeholder date that the drafter missed.

| Reason | Applies? | Why |
|---|---|---|
| Separation of concerns | No | Drafting and norm-checking don't clash; clear rules in the prompt fixed the Green/Yellow issue |
| Parallelism | No | One project per run, so no time to save |
| Independent validator | **Yes** | The author is the worst judge of its own draft |
| Context-window pressure | No | One project's data fits easily in one agent |

Everything else stays in the single agent. More splits would add coordination cost, latency, and failure points for no measurable gain.

## 2. Topology

**Pattern:** single + subagents (one validator)

```
[Monday 8:00 cron / Sev-1 hook]
   → [Cortex: pulls data, drafts update + proposes stories]
   → [Critic ✓]
        fail → back to Cortex with reasons (max 2 revisions) → escalate to me, draft held
        pass → [My review checkpoint] → queued, nothing posted
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| Cortex (chief-of-staff) | Pulls project data, drafts the weekly update, proposes stories (max 10) | M2 loop: cron + hook, stop conditions from loop-spec.md |
| Critic (validator) | Checks each draft against the 5 rules; returns pass / fail + reasons | Validation step inside the M2 loop, capped at 2 revisions |

## 4. Communication & hand-offs

- **Cortex → Critic:** the proposed draft + the source data Cortex pulled (tool results). Nothing else.
- **Critic → Cortex:** strict JSON, `{"verdict": "pass" | "fail", "reasons": [...]}`. On fail, the reasons go back to Cortex as revision feedback.
- **Cortex → me:** on pass, the draft + stories in the review queue; on cap hit, the last draft held + reasons.
- **Protocol:** plain in-process function call (`critic.review()`), no MCP / A2A needed.

## 5. The validator

- **What the critic checks** (each is a yes/no rule):
  1. **Right project + real IDs:** the update names the correct project and real PR/issue IDs from the pulled data.
  2. **No invented facts:** every number, date, and status colour traces to pulled data (Green unless an open Sev-1 or a `launch_hold` flag).
  3. **No forbidden commitments:** no unconfirmed ship date, no launch gate marked.
  4. **No leak:** no CONFIDENTIAL or embargoed item (e.g. Orbit) in the update.
  5. **Nothing actioned:** nothing posted or committed; stories only queued, max 10.
  - *Not checked on purpose:* tone and house style. In M2 the critic failed correct drafts over wording, so a human edits style.
- **Fail action (tiered):** revise, then escalate. The draft goes back to Cortex with the critic's reasons. **Revision cap: max 2**, then escalate to me with the last draft held and the reasons logged. Nothing is posted.
- **Pass action:** the draft + proposed stories go to my review queue (the HITL checkpoint from M1). Never auto-sent.

## 6. State: shared vs isolated

- **Shared:** the pulled source data and the current draft (both agents see these).
- **Isolated:** Cortex's own conversation and tool-call history stay with Cortex, so the critic judges with fresh eyes. The critic's reasoning stays with the critic; only its verdict + reasons go back.
- **Per project, per run** (from M2): nothing crosses projects, so embargoed context can't leak.

## 7. Cost & latency budget

- **Extra calls:** the critic adds 1 model call per draft. Clean run = 1 draft + 1 critic call (~$0.0015–0.002).
- **Worst case at the revision cap:** 3 drafts + 3 critic calls, ~2–3× a clean run (observed ~$0.004–0.0055). Still about 1% of the $0.50 per-run cap.
- **Latency:** each critic pass adds one model round-trip (roughly a few seconds), so worst case is ~3 extra round-trips before a draft reaches me. Fine for a Monday-morning cron, not for anything real-time.
- **Bound for M5:** keep `CORTEX_MAX_REVISIONS=2` and the $0.50 per-run cap; if the critic keeps bouncing on wording, that's a critic-quality issue to fix, not a reason to raise the cap.
