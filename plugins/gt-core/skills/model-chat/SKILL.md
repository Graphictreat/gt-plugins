---
name: model-chat
description: Multi-agent debate / shared-room pattern. Spawn N agents that iterate over R rounds — each round, every agent sees all prior-round responses, then refines, challenges, or extends. Use when you want ideas to evolve under peer pressure rather than just be aggregated. Triggers on "chat", "debate", "discuss", "round-table", "have the models argue".
---

# Model-Chat (Multi-Agent Debate)

## 1. WHEN TO INVOKE
Trigger on:
- "Have the agents debate / discuss / chat about X"
- "I want them to challenge each other on Y"
- "Round-table on Z", "do a multi-round discussion on W"
- Problems where convergence on a refined answer matters more than wide divergence

DO NOT use when:
- One independent pass is enough (use `stochastic-consensus` instead — cheaper, no rounds).
- The user just wants a fast answer.
- The topic has a single ground-truth answer reachable by checking docs/code.

`stochastic-consensus` casts a wide net once. `model-chat` lets ideas evolve under critique. Pick `model-chat` when the second-order refinement is the point.

## 2. CORE PATTERN
The "shared room" is simulated: there is no real shared chat — the orchestrator (you, in the main thread) acts as scribe, accumulating each round's outputs and feeding them into the next round's prompts.

For each round 1..R:
1. **Spawn N agents in parallel** (single message, multiple Agent calls).
2. **Collect their outputs verbatim** before starting the next round.
3. **Build the next round's prompts** by including the prior round's outputs as context.

After round R, run one **final synthesis** in the main thread.

Defaults: N=5 agents, R=3 rounds. Minimum 3 agents, 2 rounds. Cap at 6 agents, 4 rounds — beyond that the token cost balloons and returns diminish.

## 3. AGENT ROLES
Assign distinct **roles** (not just personas — roles drive *how* an agent engages with the room):
- **Proposer** — generates new ideas each round
- **Contrarian** — challenges the strongest-looking consensus
- **Synthesizer** — looks for combinations / overlaps between others' ideas
- **First-principles** — re-derives from scratch each round, ignoring momentum
- **Pragmatist** — scores ideas by feasibility and effort
- **Domain expert** (specify domain)

Assign one role per agent. Roles persist across rounds.

## 4. ROUND STRUCTURE
### Round 1 — independent priming
Each agent gets the original question + its role + no peer context.
Output shape: 5–10 ideas with one-line rationales.

### Rounds 2..R-1 — critique and refine
Each agent gets:
- Original question
- Its role
- A compact digest of all agents' prior-round outputs (verbatim ideas, abbreviated rationales)
- Instruction: "Add new ideas. Challenge ideas you disagree with — say *why*. Combine ideas where you see synergy. Drop ideas you no longer believe in — say *why*."

### Round R — converge
Same as round 2..R-1 but with this extra instruction: "Produce a final ranked list. Mark items as: foundational (must-have), recommended, experimental, reject. No new ideas."

## 5. MODEL SELECTION
- Round agents: `sonnet`. Debate needs reasoning, not just generation — haiku is too thin.
- Final synthesizer pass: main thread, or one `opus` agent if the synthesis itself is heavy.

## 6. PROMPT TEMPLATE (rounds 2+)
```
TOPIC: <original question verbatim>

YOUR ROLE: <role + one-line guidance>

PRIOR ROUND OUTPUTS (from N-1 other agents):

[Agent A — <role>]
- idea 1: ...
- idea 2: ...
[Agent B — <role>]
- idea 1: ...
...

TASK:
- Review the prior round. Identify the 2–3 strongest ideas and say briefly why.
- Add 2–5 NEW ideas your role would surface.
- Challenge 1–2 ideas you think are weak — explain the failure mode.
- If round R: produce a final ranked list, tagged {foundational | recommended | experimental | reject}.

Word budget: 500 words. Self-contained — assume I haven't seen prior rounds.
```

## 7. FINAL SYNTHESIS
After round R, write a single response with:
- **Foundational** — items every agent endorsed by round R.
- **Recommended** — items most agents endorsed; note dissents.
- **Experimental** — high-novelty items endorsed by 1–2 agents but not rejected.
- **Rejected** — items explicitly killed in debate, with reasoning.
- **Trajectory note** — one paragraph on how the discussion evolved: what changed between round 1 and round R, what surprised the room.

## 8. EXECUTION CHECKLIST
- [ ] Round-by-round: all agents spawn in parallel within a round; rounds run sequentially.
- [ ] Each round's outputs are captured before the next round's prompts are built.
- [ ] Round-N prompts include the digest of all prior rounds' outputs — not just the previous round.
- [ ] Final round explicitly asks for tagged rankings, not just more ideas.
- [ ] Synthesis reports trajectory, not just the final list.

## 9. ANTI-PATTERNS
- Running rounds in parallel (defeats the whole iteration mechanic).
- Letting one round digest grow unbounded — abbreviate rationales, keep ideas verbatim.
- Re-rolling identical agents each round (lose the role-based dispersion).
- Forgetting the trajectory note — half the value of debate is *how* views shifted.
- Using this when `stochastic-consensus` would do — model-chat costs ~R× more tokens.
