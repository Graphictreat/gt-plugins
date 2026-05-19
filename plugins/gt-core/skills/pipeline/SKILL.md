---
name: pipeline
description: Sequential specialist handoff. Decompose a task into stages, run each stage as a focused subagent with its own context (dev → review → test, or research → design → implement → QA). Use when a task has distinct phases that benefit from a clean context per stage. Triggers on "build then test", "design and review", or any multi-phase task where carrying all prior context into the next phase would degrade quality.
---

# Pipeline (Specialist Handoff)

## 1. WHEN TO INVOKE
Trigger when:
- A task has natural sequential phases (research → design → implement → review → test).
- One phase's incentives conflict with another's (developers build fast; reviewers find bugs — same agent does both poorly).
- The total work would balloon a single agent's context to the point quality degrades.
- The user says "do X then do Y then do Z" with hand-offs implied.

DO NOT use for:
- Single-phase tasks (just do them).
- Parallelizable work (use `fan-out-fan-in` instead — pipeline is strictly sequential).
- Brainstorming or option-generation (use `stochastic-consensus` or `model-chat`).

## 2. CORE INSIGHT
A single agent doing dev + review + test has conflicting incentives: building wants speed, reviewing wants suspicion, testing wants adversarial coverage. Different specialist agents with focused prompts outperform one generalist — even when the generalist is the more capable model — because each starts fresh and is shaped for one job.

Additionally, each stage starts with a clean context window: no prior dev chatter polluting the reviewer's signal.

## 3. CORE PATTERN
1. **Decompose** the task into ordered stages. Confirm the decomposition with the user in one short sentence before spawning, unless the user already specified the stages.
2. **Define an artifact contract** between stages — what does stage N hand to stage N+1? File paths, structured findings, a written summary?
3. **Run stages sequentially**. Each stage = one Agent call. Capture the output before launching the next.
4. **Pass artifacts forward** — each stage's prompt includes the prior stage's artifact verbatim (or a tight summary if huge).
5. **Final integration** in the main thread: report what each stage produced, surface unresolved issues.

Pipelines are sequential by definition. Do NOT spawn stages in parallel. If two stages could run in parallel, they aren't really pipeline stages — use `fan-out-fan-in`.

## 4. COMMON PIPELINES
- **Build → Review → Fix**: dev agent ships a change; reviewer agent inspects; fixer agent applies review notes.
- **Research → Design → Implement**: research surfaces options; design picks one and writes a spec; implementer codes from spec.
- **Spec → Build → Test → Document**: spec agent writes the contract; build agent codes it; test agent writes tests; doc agent updates README/comments.
- **Reproduce → Diagnose → Fix → Verify**: bug-repro agent; root-cause agent; patch agent; verification agent.

These are starting points — adapt to the task. Don't force a task into one of these shapes if it doesn't fit.

## 5. STAGE PROMPT TEMPLATE
Each stage agent gets:
- **Goal of this stage** — one sentence ("Your job is to review the diff produced by the prior stage.")
- **Inputs** — the artifact(s) from the prior stage, inline or referenced by path
- **Output contract** — what shape the artifact you produce must take (file paths written? structured findings? a summary?)
- **Boundaries** — what this stage MUST NOT do ("Do not modify code — only produce findings." "Do not add new features — only fix what the reviewer flagged.")
- **Self-containment** — assume you've never seen this conversation

## 6. MODEL SELECTION PER STAGE
Match model to stage character:
- **Research / brainstorming**: `sonnet`
- **Implementation**: `sonnet` (most code work) or `opus` (complex refactor)
- **Review / bug-finding**: `opus` — reviewers need depth, not breadth
- **Testing / verification**: `sonnet`
- **Documentation**: `haiku` or `sonnet`

Don't reflexively use `opus` everywhere. Use it where reasoning depth pays off (review, complex synthesis).

## 7. ARTIFACT HANDOFF
Two ways to pass artifacts between stages:
1. **In-prompt** — the prior stage's full text output is pasted into the next stage's prompt. Good for findings, plans, short specs.
2. **On-disk** — the prior stage writes files; the next stage's prompt references file paths. Good for code, large specs, anything multi-file.

Prefer on-disk for code changes (the reviewer reads the actual diff, not a description of it). Prefer in-prompt for short structured findings.

## 8. STAGE-BOUNDARY DISCIPLINE
- Don't let a stage do the next stage's job. If the implementer also "reviews itself", you've collapsed the pipeline.
- Don't loop stages quietly. If review finds bugs and you run a fix stage, that's a planned step — call it out, don't pretend the pipeline was straight-line.
- If a stage's output is unusable, STOP and report to the user. Don't paper over with a quick fix in the main thread — that re-introduces the conflict-of-interest problem the pipeline exists to solve.

## 9. OUTPUT
After the final stage, report:
- **Stage-by-stage summary** — one short paragraph per stage, what it produced.
- **Final artifact location** — file paths or summary.
- **Open issues** — anything any stage flagged that wasn't resolved by a later stage.
- **What was skipped** — if a planned stage was unnecessary, say so explicitly.

## 10. EXECUTION CHECKLIST
- [ ] Stages decomposed and confirmed with user (one sentence) before spawning.
- [ ] Artifact contract defined for each stage boundary.
- [ ] Stages run strictly sequentially — one Agent call at a time.
- [ ] Each stage prompt is self-contained, with prior artifact embedded or referenced.
- [ ] Model per stage chosen deliberately, not defaulted to opus everywhere.
- [ ] Final report lists each stage's output and any unresolved issues.

## 11. ANTI-PATTERNS
- Running stages in parallel (that's `fan-out-fan-in`, not a pipeline).
- One mega-agent doing all stages (the exact thing this skill exists to avoid).
- No artifact contract — each stage guesses what the prior one meant.
- Silent retries / loops between stages (the user should see when review → fix → re-review happens).
- Forcing every task into a 4-stage pipeline when 2 stages would do.
