---
name: stochastic-consensus
description: Stochastic multi-agent consensus. Spawn N agents in parallel with slightly different personas/framings, have each produce M independent ideas, then aggregate by frequency — high-vote ideas are consensus, single-vote ideas are outliers. Use for brainstorming, option generation, ranking, strategic analysis, or any problem where you want to filter hallucinations and surface the search space.
---

# Stochastic Multi-Agent Consensus

## 1. WHEN TO INVOKE
Trigger on language like:
- "Brainstorm all the ways to ..."
- "What are the options for ..."
- "Poll agents on ...", "stochastic consensus ...", "vote on ..."
- "Generate ideas for ...", "explore the solution space for ..."
- Strategic / design / business decisions with many viable paths

NOT for:
- Problems with one correct answer (use direct reasoning)
- Tasks the user wants executed, not deliberated
- Investigation of a known codebase fact (use grep/Explore)

## 2. CORE PATTERN
1. **Spawn N agents in parallel**, each with a slightly different persona/framing but the SAME core question. Default N=8; minimum 5; cap 15.
2. **Each agent returns M ideas** (default M=10) with one-line rationales. Self-contained prompts — they cannot see this conversation.
3. **Aggregate by frequency**:
   - Normalize ideas (lowercase, strip filler) and cluster near-duplicates as the same item.
   - Count votes per cluster (the **mode** — how many agents proposed it).
4. **Report tiers**:
   - **Consensus** (vote ≥ N/2): high-confidence, low-novelty.
   - **Mid-band** (vote 2 to N/2-1): common but not universal.
   - **Outliers** (vote = 1): unique angles — these are often the most interesting; do NOT discard.

## 3. PERSONA MENU
Pick N personas with maximum dispersion. Sample set — adapt per domain:
- Conservative / tradition-minded
- Adventurous / boundary-pushing
- Contrarian / challenges conventional wisdom
- First-principles reasoner
- Pragmatist / cost-and-effort optimizer
- Domain expert (specify domain)
- Generalist outsider with adjacent-field experience
- Skeptic / what-can-go-wrong lens
- Maximalist / "ignore constraints"
- Minimalist / "what's the cheapest version"

Never use the same persona twice. Persona drives dispersion — without it you just get N copies of the same list.

## 4. MODEL SELECTION
- Idea-generation agents: `sonnet` (good cost/quality balance for divergent thinking). Use `haiku` only if you need >12 agents and budget matters.
- Aggregation: in the main thread. The aggregation logic is mechanical (cluster + count); reasoning is light.

## 5. AGENT PROMPT TEMPLATE
Each agent receives:
- The original question verbatim
- The persona assignment in one sentence ("You are a contrarian challenging conventional wisdom.")
- "Produce at least M distinct ideas. Each idea: name + one-line rationale. No numbering required."
- "Do not pull punches; do not water down ideas to be safe."
- Word budget — keep tight so they generate breadth, not depth

## 6. AGGREGATION RULES
- Cluster aggressively but not lazily. "Roasted tomatillo salsa" and "fire-roasted tomatillo verde" are the same cluster; "roasted tomatillo salsa" and "raw tomatillo agua chile" are not.
- When clustering ideas with the same name but different rationales, KEEP both rationales — they add nuance.
- Vote counts are based on cluster membership, one vote per agent per cluster (an agent can't double-count).
- Report total raw ideas and total clusters, so the user can see compression ratio.

## 7. OUTPUT SHAPE
```
## Consensus (≥ N/2 votes)
1. <Idea> — votes: X. <merged rationale>
...

## Mid-band (2 to N/2-1 votes)
- <Idea> — votes: X. <merged rationale>
...

## Outliers (1 vote — highest-novelty)
- <Idea>. <rationale>. (Agent: <persona>)
...

## Stats
Agents: N. Raw ideas: R. Unique clusters: U. Compression: R/U.
```

## 8. EXECUTION CHECKLIST
- [ ] All Agent calls sent in ONE message (parallel).
- [ ] N personas, all distinct.
- [ ] Each agent prompt is self-contained — restates the question, no "as discussed".
- [ ] Aggregation is by frequency, not by which idea sounds best to you.
- [ ] Outliers are reported, not silently dropped — they are the high-novelty payload.
- [ ] Stats (N, R, U) are reported at the end.

## 9. ANTI-PATTERNS
- Identical persona prompts (you'll get N copies of the same list).
- Quietly dropping outliers because they look weird — they are the whole point.
- Letting one agent decide for the group (that defeats the consensus mechanic).
- Skipping the clustering step and reporting raw counts (inflates consensus).
- Using this for problems that have one right answer (waste of tokens).
