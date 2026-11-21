# Pitch Talk Track: Cortex, a PM Chief-of-Staff Agent

> How I present [`pitch.html`](pitch.html), slide by slide. Target: about 5 minutes. One idea per slide; speak to the decision, not the text on screen.

---

## Slide 1 · Cover (~30 sec)

**On screen:** title, one-line pitch, my name, "View the repo."

**Say:**
> "Every PM spends hours each week turning engineering activity into a leadership update. Cortex is my chief-of-staff agent that drafts it from real data, has an independent critic check it, and queues it for my approval. It never posts on its own. I'll show you what it owns, how it's bounded, and the number that would let it do more."

**Tip:** the last sentence is the promise; every slide after it delivers on it.

---

## Slide 2 · The 6 building blocks (~60 sec)

**On screen:** six module cards with links. Say one line per module, the decision, not the title:

- **M1:** "I drew the line: Cortex drafts and proposes; posting, dates, and escalation stay human."
- **M2:** "It runs Monday 8am plus on any Sev-1, and stops on success, when stuck, or to escalate."
- **M3:** "I resisted building a team: one agent plus one independent critic, because it can't grade its own draft."
- **M4:** "It only sees this project's data, never the embargoed Orbit or Pulsar."
- **M5:** "Seven hard limits outside the model: steps, timeout, cost, story cap, no post tool, kill switch, human checkpoints."
- **M6:** "And a plan to ship it and earn trust, which is the next slide."

**Tip:** if asked, click one link live (e.g. M5) to show it's real work.

---

## Slide 3 · How I'd ship Cortex (~2 min, the key slide)

Walk the five cards left to right, about 20 seconds each.

- **01 Dial:** "Autonomy is per user, not one switch. A new eng lead approves everything; a seasoned PM could just spot-check the routine update; execs only ever see PM-approved updates. And the dial never moves the agent line."
- **02 Ladder** (slow down, this is the honesty moment): "Today Cortex is at Rung 3, supervised. To climb to Rung 4 it needs 4 weeks and 30+ runs at ≥ 90% completion, ≥ 95% tool accuracy, zero unsafe steps, and zero incidents. It hasn't met that gate yet: the same task passed at 17:05 and was held at 19:03. I fixed the cause; now it has to earn the window."
- **03 Deploy:** "Serverless for the cron and the hook. I'm the named owner. If it misbehaves: kill switch, revert, remove a tool, or drop the dial a rung."
- **04 ROI:** "I measure value, not clicks: about 45 minutes saved per project per week, under 20 minutes cost per update, mostly my review time rather than the half-cent of model cost, and zero incidents."
- **05 Governance:** "Confidential projects and PII never enter a prompt. Posting, dates, and story batches stay human at every setting. If the model's down, it fails closed."

---

## Slide 4 · What I learned (~60 sec)

Say it personally; this is the most memorable slide.

- **Friction:** "The hardest part wasn't the AI. It was the critic. Every fix was in the rules I'd given it."
- **Learning:** "Capability is not permission. Every jailbreak failed, not because of a clever prompt, but because there's no post tool to abuse."
- **Aha** (pause before it): "Same task, two hours apart: pass, then held. One good demo proves nothing. Autonomy is earned with a number over a window."

---

## Close (~15 sec)

> "So Cortex isn't 'an AI that writes updates.' It's an agent with a clear line, hard bounds, and a measurable path to more autonomy, and right now it's honestly at supervised. Happy to show a live run."

---

## Optional live demo (~60 sec)

- Run `python agent.py jailbreak` in `00-build/`: "Watch: it names the attack and posts nothing." Short, reliable, and dramatic, so it's the best demo.
- Avoid the happy path live: it can get held, and it's slower.

---

## Likely questions

| Question | Answer |
|---|---|
| "Why not just let it post?" | "Posting is irreversible with a high blast radius. It stays above the line at every dial setting." |
| "How do you know the critic is right?" | "I don't fully. It nitpicked wording, so I scoped it to facts, norms, and safety. That's why there's an eval gate plus human review." |
| "What's the real ROI?" | "About 45 minutes a week per project; model cost is negligible, and the real cost is review time." |
| "Why only one subagent?" | "Every split costs coordination and latency. Only the validator earned its place." |
| "What's next?" | "Collect the 30-run window, then a read-only Jira connector in shadow mode." |

---

## Delivery tips

1. Say the numbers out loud (4 weeks, 30 runs, 90%, Rung 3); they make it credible.
2. Lead with the honesty ("gate not met yet"); in this course it scores, it doesn't hurt.
3. Don't read the cards. Point at them and say the why.
