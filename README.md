# Cortex: PM Chief-of-Staff Agent, by Varun Patel

> A PM chief-of-staff agent that turns a project's real engineering activity into a grounded weekly leadership update and a capped story batch, checked by an independent critic and queued for my approval, never posted on its own.

_Varun Patel · Agentic Workflows & Loops Certification · 2026_

Repo: https://github.com/varunpateltech/pm-os-agent · **Pitch deck:** [`06-autonomy/pitch.html`](06-autonomy/pitch.html) (download and open in a browser)

This repo is my final project for the Agentic Workflows & Loops Certification, **Cortex**. Each module's artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artifacts

### M1 · The Agent Line
- **Agent-line map**: [`01-agent-line/agent-line-map.md`](01-agent-line/agent-line-map.md)

### M2 · Loop Engineering
- **Loop spec**: [`02-loop-design/loop-spec.md`](02-loop-design/loop-spec.md)

### M3 · Orchestration & Subagents
- **Orchestration map**: [`03-orchestration/orchestration-map.md`](03-orchestration/orchestration-map.md)

### M4 · Choose What Each Agent Step Is Allowed to See
- **Memory & context plan**: [`04-memory-context/memory-and-context.md`](04-memory-context/memory-and-context.md)

### M5 · Bounds & Evals
- **Bounds & evals**: [`05-bounds-evals/bounds-and-evals.md`](05-bounds-evals/bounds-and-evals.md)

### M6 · Autonomy & Production
- **Production & autonomy plan**: [`06-autonomy/production-and-autonomy.md`](06-autonomy/production-and-autonomy.md)
- **Prototype write-up** (with run screenshots): [`06-autonomy/prototype.md`](06-autonomy/prototype.md)
- **Build insights**: [`06-autonomy/build-insights.md`](06-autonomy/build-insights.md)

---

## Ship plan

### Autonomy dial (per segment)
New eng lead ("Tesla driver") → Rung 3, Supervised: approves every draft and story batch. Seasoned PM ("Waymo passenger") → Rung 4, Bounded-autonomous, routine weekly update only: spot-checks. Exec stakeholder → Rung 2, Assisted: sees only PM-approved updates. The dial never moves the agent line. These are targets: today Cortex is at Rung 3 for everyone.

### Trust Ladder rung + eval gate
Today: Rung 3, Supervised. Cortex prepares everything; nothing goes out until I approve. Gate to Rung 4 (seasoned PM, routine update): over 4 consecutive weeks AND 30+ supervised runs, task completion ≥ 90% with no fact changes, tool-call accuracy ≥ 95%, 0 unsafe steps, and jailbreak + missing-data replays passing 100%, with 0 trust incidents. Status: gate not met yet. The same task passed at 17:05 and was held at 19:03 for proposing already-shipped stories; fixed with one prompt rule, now collecting the window.

### Deployment plan
Runtime: serverless, a scheduled function for the Monday 08:00 cron and an event-triggered function for the Sev-1 hook; runs are under 2 minutes with no state between them. Owner: Varun Patel (PM), with the Northstar eng lead as escalation for infra failures. Rollback, in order: kill switch, git revert the last prompt or code change, remove a tool, drop the dial a rung. Monitoring: eval pass %, critic rejection rate, escalation rate, cost per run and per day, trust incidents.

### ROI metrics + widen-autonomy rule
Outcome: ≥ 90% of updates approved with no fact changes. Time saved: ~45 min/week per project (manual ~60 min → review ~15 min), measured against a shadow-mode baseline. Cost-to-serve: under 20 min per approved update, about $0.005 of model cost plus my review time. Trust incidents: 0 per 4-week window. Widen rule: the dial goes up one notch, for one segment only, when the gate holds for 4 weeks and 30+ runs with 0 incidents; one incident drops it back a rung.

### Governance & strategy
Compliance: confidential roadmap items (Orbit, Pulsar), customer PII and credentials never enter a prompt. Safety: posting, ship dates and story batches stay above the line at every dial setting; kill switch always available. Reliability: 8 steps, 120 s, $0.50 per run, $2 per day, 2 revisions, 10 stories; escalate when stuck; if the model is down, no draft and the PM is notified. Next: seasoned PM to Rung 4, then a read-only Jira connector after 2 weeks in shadow mode on real data.

---

## Build insights

- **Friction point.** The critic. It flip-flopped between Green and Yellow, failed correct drafts over wording, and held a good update for proposing stories that had already shipped. Each time the fix was in the rules I'd given it, not the model.
- **Key learning.** Capability is not permission. The strongest safety isn't a clever prompt but a missing tool: Cortex can't post because no post tool exists, which is why every jailbreak failed. Bounds that live outside the model held every time; prompt rules sometimes didn't.
- **Aha moment.** The same task passed at 17:05 and was held at 19:03. One good run proves nothing, so autonomy has to be earned with a number over a window, not given because a demo looked good.

---

## How to run it

```bash
cd 00-build
pip install -r requirements.txt
cp .env.example .env    # add your OPENAI_API_KEY; on Windows: copy .env.example .env
python agent.py         # happy path; also: python agent.py missing-data · python agent.py jailbreak
```

Every run ends with a draft held for human review; nothing is ever posted. Full steps, probes, and the kill switch are in [`06-autonomy/prototype.md`](06-autonomy/prototype.md#how-to-run-it).

---

_Certification submission, Agentic Workflows & Loops Certification._
