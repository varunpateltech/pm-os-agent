# Build Insights: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 4, what you learned building it
>
> ✅ **What this validates:** you can reflect on what building it taught you, by the end you'll have proven the friction, the learning, and the aha that changes how you'd design your next agent.

## Friction

The critic. It went back and forth between Green and Yellow, failed correct drafts over wording, and once held a good update for proposing stories that had already shipped. Each time I had to read the trace to work out whether the drafter or the critic was wrong. Usually it was the rules I'd given them. Second: the terminal. Windows PowerShell vs CMD syntax cost me more time than any agent concept.

## Learning

1. **Capability is not permission.** The strongest safety isn't a clever prompt but a missing tool: Cortex can't post because no post tool exists, which is why every jailbreak failed.
2. **A bound has to live outside the model.** Counters, timers, budgets, and a kill switch held every time; prompt rules sometimes didn't.
3. **Grounding beats intelligence.** When I swapped in new data (41% → 43%), Cortex followed the data, and when a project didn't exist, it escalated instead of inventing.

## Aha moment

The same task can pass at 17:05 and be held at 19:03. One good run proves nothing, so autonomy has to be earned with a number over a window, not given because a demo looked good.

## What you'd do differently

Write the eval cases first (Evals = the PRD), before tuning prompts, and fix the critic's scope early so it judges facts, not wording. I'd also start Cortex in shadow mode on real data from day one, to build the evidence window sooner.
