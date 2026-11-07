# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> ✅ **What this validates:** every risky action has a clear owner, by the end you'll have proven an above/below-the-line map with HITL checkpoints, scored on reversibility, blast radius, and measurability.

## The workflow, decision by decision

List every discrete decision or action in your agent's workflow, then score each one and place it **above** the line (a human owns it) or **below** (the agent owns it). Borderline calls get an HITL checkpoint.

| Decision / action | Reversibility (H/M/L) | Blast radius (H/M/L) | Measurability (H/M/L) | Above / Below | HITL? |
|---|---|---|---|---|---|
| Pull project state + recent GitHub/Jira activity | H | L | H | Below | · |
| Decide relevant context | H | M | M | Below | spot-check |
| Draft the weekly leadership status update | H | M | M | Below | spot-check |
| Decide tone / commitment level | L | H | L | Above | · |
| Flag at-risk / escalation | H | M | M | Below | spot-check |
| Choose what to escalate | L | H | M | Above | · |
| Propose next sprint's stories (capped at 10) | H | M | M | Below | required |
| Post the update / approve a company-wide one | L | H | M | Above | required |

## Agent anatomy (sketch)

- **Model:** gpt-4o-mini by default (cheap, fast, ~$0.004 per run). Escalate to a frontier model (e.g. gpt-4o) when the critic keeps rejecting the draft on a judgment call, as in my first run, where 4o-mini kept calling an on-track project Yellow and hit the iteration cap.
- **Tools:** get_project · get_activity (read) · search_past_updates · get_roadmap · get_norms · propose_stories (capped at 10, queued for review). No send/post tool, posting stays human.
- **Memory:** persists across runs: roadmap, past updates, decision log, team norms. Purged per run: the task, the draft, and the critic's feedback.
- **Loop:** _placeholder, defined in M2 loop-spec.md_
- **Bounds:** _placeholder, defined in M5 bounds-and-evals.md_
- **Evals:** _placeholder, defined in M5 bounds-and-evals.md_

## The golden rule, applied

- **Pull project state + activity** sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify, deciding factor: reversibility (read-only, nothing changes).
- **Decide relevant context** sits below the line with a spot-check because it's easy to reverse but has a medium blast radius, deciding factor: blast radius (a wrong pick, like the embargoed Orbit, could leak into the draft).
- **Draft the update** sits below the line with a spot-check because a draft is easy to reverse and is only harmful if sent, deciding factor: reversibility.
- **Decide tone / commitment level** sits above the line because a stated commitment is hard to reverse, has a high blast radius, and is hard to verify until the date slips, deciding factor: measurability.
- **Flag at-risk / escalation** sits below the line with a spot-check because a flag is only a signal to the PM and is easy to dismiss, deciding factor: blast radius (a missed flag is the real risk).
- **Choose what to escalate** sits above the line because you cannot un-escalate and it carries political judgment, deciding factor: reversibility.
- **Propose next sprint's stories** sits below the line with required approval because it's a capped proposal that's easy to reject, deciding factor: reversibility (nothing enters a sprint without a human).
- **Post the update / approve company-wide** sits above the line with required approval because once posted it can't be un-read and the whole company sees it, deciding factor: blast radius.

## Hardest call

**Decide relevant context.** My first instinct scored it H/L/H: picking context changes nothing, so it felt like a clean below-the-line call. Then I realised Northstar sits next to Orbit, an embargoed project. If Cortex pulls the wrong context, confidential info lands in a leadership draft. I kept it below the line, but re-scored it H/M/M and added a spot-check. **Settling axis: blast radius.** Reversibility alone made it look safe; what it could leak is what made it borderline.
