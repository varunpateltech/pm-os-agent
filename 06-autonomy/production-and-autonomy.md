# Production & Autonomy: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 5, how you'd ship it, govern it, and widen trust over time
>
> ✅ **What this validates:** you can ship it, govern it, and widen trust deliberately, by the end you'll have proven an autonomy dial, a Trust Ladder rung with its eval gate, and a governance plan.

## Autonomy Dial by segment

The dial sets how many below-the-line actions still pause at a HITL checkpoint for each user. It never moves the M1 agent line: posting an unapproved company-wide update, committing a ship date, and issuing story batches stay above the line for everyone.

| Segment | Desired autonomy | Why |
|---|---|---|
| **New eng lead** (first weeks with Cortex, "Tesla driver") | **3 · Supervised**: every draft and story batch waits for their approval | New to Cortex; wants to see and approve each output before anything moves |
| **Seasoned PM** (owns the project, months of correct drafts, "Waymo passenger") | **4 · Bounded-autonomous**, for the routine weekly update only | Trusts the format and spot-checks instead of approving each one; story batches and date questions still always pause |
| **Exec stakeholder** (receives updates, doesn't run Cortex) | **2 · Assisted**: sees only PM-approved updates | Cortex never sends anything to execs on its own; a PM always stands between them |

These are target levels. Today Cortex is at rung 3 (supervised) for every segment; the seasoned PM's rung 4 unlocks only after the eval gate in the Trust Ladder section is met.

## Trust Ladder

- **Current rung: 3 · Supervised.** Cortex prepares the full update and story batch, but nothing goes out until a human approves it: every run ends "queued for your review, nothing posted."
- **Eval gate to reach rung 4** (routine weekly update, seasoned PM only): over **4 consecutive weeks AND at least 30 supervised runs**:
  - **Task completion ≥ 90%:** critic passes and the PM approves without changing any fact (EV-4)
  - **Tool-call accuracy ≥ 95%:** right tools, right project (EV-1)
  - **Path quality: 0 unsafe steps** (EV-2)
  - **Jailbreak and missing-data replays pass 100%** on every change (EV-5, R-3, R-4)
  - Why a run count as well as a window: one weekly update × 4 weeks is only 4 runs, too few to measure a percentage.
- **Clean incident record for that window:** **0 trust incidents**: nothing posted without approval, no confidential leak (Orbit, Pulsar), no ship date committed, no invented metric reaching the PM.
- **Incident record so far:** 0 trust incidents across all course runs (M2–M6). Every jailbreak and withheld-source probe escalated, and nothing was ever posted.
- **Gate status today: not met.** The same happy-path task passed the critic at 17:05 but was held at 19:03 on 2026-11-21 after proposing stories that had already shipped (#820, #823). The plan: add a critic check that every proposed story is not already merged, then collect the 30-run window.

## Deployment plan

- **Runtime: serverless.** A scheduled function runs the Monday 08:00 **cron**; an event-triggered function runs the Sev-1 **hook** (my M2 loop types). Each run is short (under 2 minutes) and holds no state between runs, so pay-per-run fits better than an always-on service.
- **Operator / on-call owner:** **Varun Patel (PM)** owns Cortex in production; the **Northstar eng lead** is the escalation path for infrastructure failures.
- **Rollback**, in order of severity:
  1. Turn on the kill switch (create `00-build/KILL`): no run starts and a running loop halts at its next step.
  2. `git revert` the last prompt or code change.
  3. Remove a misbehaving tool from the tool list in `agent.py`.
  4. Drop the dial one rung for the affected segment.
- **Monitoring:** eval pass % · critic rejection rate · escalation rate · cost per run and per day · trust incidents.

## ROI metrics (beyond adoption & tokens)

| Metric | Target | How it's captured |
|---|---|---|
| **Outcome:** updates approved with no fact changes | ≥ 90% | PM approval log compared with the draft |
| **Time saved** | ~45 min/week per project (manual ~60 min → review ~15 min) | PM times 2 manual updates as a shadow-mode baseline, then times each review |
| **Cost-to-serve** per approved update | < 20 min total: ~$0.005 model cost + PM review time | Run cost from the logs + review time |
| **Trust incidents** | 0 per 4-week window | Run logs + critic verdicts + PM flags |

The real cost is my review time, not the model (about half a cent per run).

## Widen-autonomy decision rule

The dial goes up one notch, for one segment only, when the Trust Ladder eval gate holds for 4 consecutive weeks and 30+ runs with 0 trust incidents; one incident drops it back a rung until the gate is re-cleared.

## Governance & forward strategy

- **Compliance:** never in a prompt: CONFIDENTIAL roadmap items (Orbit, Pulsar), customer PII, credentials or API keys. Roadmap retrieval drops anything marked CONFIDENTIAL (M4), and memory stores the minimum per project.
- **Safety:** above the agent line for everyone, at every dial setting: posting company-wide, committing ship dates, issuing story batches. The kill switch is always available to the owner.
- **Reliability:** caps of 8 steps · 120 s · $0.50 per run · $2 per day · 2 revisions · 10 stories; escalate when stuck; if the model is down, Cortex produces no draft and notifies the PM (fail closed).
- **Strategy:** next segment: the seasoned PM's routine weekly update to rung 4, gated by the Trust Ladder eval gate. Next capability: a read-only Jira connector, gated by the replay set passing on real data in shadow mode for 2 weeks.
