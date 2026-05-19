---
name: fan-out-fan-in
description: Fan-out / fan-in research pattern. Spawn N cheap parallel researcher subagents on different angles of a question, then synthesize their outputs with one stronger model. Use for "research X", "find best Y", "compare A/B/C", "how should I optimize Z", or any open-ended investigation where breadth + speed matter more than serial depth.
---

# Fan-Out / Fan-In

## 1. WHEN TO INVOKE
Trigger this skill when the user asks for:
- Open-ended research ("find the best APIs / libraries / approaches for X")
- Multi-angle analysis of a codebase, design, or problem
- "Compare", "survey", "explore", "what are my options for"
- Optimization investigations where many independent axes exist

DO NOT use for:
- Single-file edits or trivial lookups (use direct tools)
- Problems with a single right answer reachable by one search
- Tasks the user wants done sequentially with their input between steps

## 2. CORE PATTERN
1. **Decompose** the question into N independent angles (default: 5; minimum 4; cap at 10 unless the user asks for more).
2. **Fan out** — spawn one researcher subagent per angle. ALL Agent calls MUST go in a SINGLE message with multiple tool-use blocks so they execute in parallel.
3. **Fan in** — once every researcher returns, run ONE synthesizer pass (in the main thread) that integrates overlaps, surfaces outliers, and ranks by confidence.

## 3. MODEL SELECTION
- Researchers: `sonnet` by default. Use `haiku` if the angle is pure data-extraction with little reasoning. Reserve `opus` for researchers only when the user explicitly asks for max quality.
- Synthesizer: run in the main thread (whatever model the user is on) — no extra Agent call needed unless the synthesis itself is heavy. If it is, spawn one final agent with `opus`.

Rationale: researchers consume the bulk of tokens but rarely need top-tier reasoning; synthesis is short but needs the strongest model to integrate well.

## 4. ANGLE DECOMPOSITION
Good angles are **orthogonal** — minimal overlap maximizes coverage. Pick from this menu and adapt:
- **Domain axes**: performance, security, DX, cost, maintainability, accessibility
- **Stakeholder lenses**: end-user, operator, future-maintainer, attacker
- **Method lenses**: first-principles, prior-art, contrarian/devil's-advocate, ecosystem-survey
- **Layer lenses**: data, network, render, build, runtime

If the user supplies their own decomposition, use theirs verbatim. Never duplicate angles.

## 5. RESEARCHER PROMPT TEMPLATE
Each researcher gets a self-contained prompt — they cannot see this conversation. Include:
- The original question verbatim
- The specific angle assigned ("Focus on X. Do NOT cover Y or Z — other agents handle those.")
- Required output shape: bulleted findings + a short rationale per bullet + a confidence note
- Word budget (e.g., "under 400 words")
- Whether to use web search, read files, run commands, or all of the above

Use `subagent_type: "general-purpose"` for code/repo research. Use `subagent_type: "Explore"` only when the angle is purely "find references / locate definitions" — Explore is read-only and skims, so it's wrong for analysis.

## 6. SYNTHESIZER STEP
After all researchers return, produce a single response with this shape:
1. **Consensus findings** — points raised by ≥2 researchers, ranked by frequency.
2. **High-signal outliers** — unique points from a single researcher that look load-bearing. Flag them as outliers explicitly.
3. **Disagreements** — places where researchers contradict each other. Show both sides; do NOT arbitrate unless one side has clearly stronger evidence.
4. **Recommendation** — one paragraph, opinionated, citing which findings drove the call.

Never just concatenate researcher outputs. Integrate.

## 7. EXECUTION CHECKLIST
- [ ] Question decomposed into ≥4 orthogonal angles (told the user the angle list before spawning, in one short sentence).
- [ ] All Agent calls sent in ONE message (parallel, not serial).
- [ ] Each researcher prompt is self-contained — no references to "the conversation" or "as we discussed".
- [ ] Researchers got an output-shape contract + word budget.
- [ ] Synthesis integrates, does not concatenate.
- [ ] Outliers and disagreements are surfaced, not silently dropped.

## 8. ANTI-PATTERNS
- Spawning agents serially (kills the speed advantage).
- Using `opus` for routine researchers (kills the cost advantage).
- Synthesizer that just lists "Agent 1 said... Agent 2 said..." (kills the quality advantage).
- More than ~10 agents on a small question (diminishing returns, longer wait on the slowest).
- Overlapping angles (you'll just see the same answer N times).
