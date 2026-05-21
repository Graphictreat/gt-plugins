---
name: x-post-gen
description: Generate a high-engagement, interesting, educational X (Twitter) post using a sequential pipeline (brief → draft → playbook critique → polish). Encodes a research-and-debate-derived 2026 X playbook with universal rules + market-specific overlays. Use when the user asks to "write an X post", "draft a tweet", "make a Twitter post about X", or wants help producing a post optimized for the X algorithm. Takes a topic + optional market (b2b-saas / indie / creator / crypto / science / generic) + optional format (single / thread).
---

# x-post-gen (Educational X Post Generator)

## 1. WHEN TO INVOKE
Trigger when:
- User asks to write, draft, or generate an X (Twitter) post.
- User wants a "tweet" or "X thread" about a specific topic.
- User asks to optimize or rewrite an existing draft for X.

DO NOT use for:
- LinkedIn, Threads, Bluesky, or other platforms (different algorithm + audience).
- Long-form blog posts or essays (use a writing skill instead).
- Internal-only / private content (this skill optimizes for public reach).

## 2. CORE INSIGHT
The post-quality problem on X in 2026 splits into two clean phases that conflict if one agent does both: **drafting** (generative, voice-led, willing to ramble) and **critique** (suspicious, checklist-driven, algorithm-aware). One agent doing both produces either a safe-but-flat post or a punchy-but-rule-breaking one. A 4-stage pipeline separates the incentives.

## 3. PIPELINE STAGES

The skill runs **4 sequential stages**, each as one Agent call. Do NOT spawn in parallel.

### Stage 1 — BRIEF & ANGLE (sonnet)
**Goal:** Convert the user's raw topic into a postable angle: identify the gap, the specific detail to lead with, the target reader's recurring situation for the CTA.

**Inputs:** user topic, market lens, format (single/thread), any constraints.

**Output contract (in-prompt artifact):**
```
ANGLE: <1 sentence — what specific claim or insight will the post deliver>
TARGET READER: <who, and what they currently believe>
THE GAP: <what they believe vs what the post will reveal>
LEAD SPECIFIC: <the number / named entity / dated event the post will open with>
READER'S RECURRING SITUATION (for CTA): <a moment the reader actually inhabits>
VEHICLE: <number-first | story-first | loss-first | falsifiable-claim>
MARKET OVERLAY: <which market-specific rule applies — see Section 5>
```

### Stage 2 — DRAFT (sonnet)
**Goal:** Produce 3 candidate posts using different vehicles from Stage 1's brief.

**Inputs:** Stage 1 brief verbatim.

**Output contract:**
```
DRAFT A (vehicle: <X>):
<post text — single post under 280 chars, or thread with numbered posts>
CHAR COUNT: <n>

DRAFT B (vehicle: <Y>):
<post text>
CHAR COUNT: <n>

DRAFT C (vehicle: <Z>):
<post text>
CHAR COUNT: <n>
```

**Boundaries:** Do not critique. Do not pick a winner. Generate 3 distinct vehicles.

### Stage 3 — PLAYBOOK CRITIQUE (opus)
**Goal:** Score each draft against the playbook (Section 4). Identify AI-slop tells. Flag rule violations. Recommend ONE winner with a fix list.

**Inputs:** Stage 1 brief + Stage 2 drafts verbatim + the playbook below.

**Output contract:**
```
DRAFT A SCORE: <pass/fix/reject>
- Universal rule 1 (gap): <pass | issue>
- Universal rule 2 (specific): <pass | issue>
- Universal rule 3 (real-situation CTA): <pass | issue>
- Market overlay: <pass | issue>
- AI-slop tells: <none | list them>
- Fixes: <bullet list of concrete edits>

[same for DRAFT B, DRAFT C]

WINNER: <A | B | C>
RATIONALE: <2 sentences>
REQUIRED FIXES: <bullet list to apply in Stage 4>
```

**Boundaries:** Do not rewrite the drafts. Only score and prescribe fixes.

### Stage 4 — POLISH & FINALIZE (sonnet)
**Goal:** Apply the critique's required fixes to the winning draft. Produce the ready-to-post text + posting metadata.

**Inputs:** Stage 1 brief + winning draft + Stage 3 fix list.

**Output contract:**
```
FINAL POST:
<verbatim text, exactly as it should be posted>

CHAR COUNT: <n>

POSTING METADATA:
- Best window: Tue–Thu, 9 AM–1 PM (user's local audience time zone)
- Native media suggestion: <image / <60s video / none — and what it should show>
- First-reply content: <if a link, paste the URL plan here; otherwise note "none">
- Self-reply plan for first 30 min: <1-2 sentences on how to respond to early comments>

POSTING CHECKLIST (run before posting):
- [ ] First 120 chars hook with specific detail
- [ ] No external links in the main post
- [ ] ≤ 2 hashtags (only if niche-specific)
- [ ] No em-dash decoration / "Let me explain" / "It's not just X, it's Y" / rocket-emoji bullets
- [ ] CTA names a real situation the reader inhabits
- [ ] Author available to reply for 30 min after posting
```

**Boundaries:** Do not regenerate the post wholesale. Apply only the fixes the critique prescribed. If the fixes conflict, flag it and stop.

## 4. THE PLAYBOOK (passed into Stage 3 verbatim)

### Universal Rules (all markets)
1. **Open with a gap, not a claim.** Sentence 1 must create distance between what the reader currently believes and what the post will deliver. Premises and summaries get scrolled.
2. **Lead with one concrete number or named specific in the first 2–3 lines.** Numbers, dates, dollar amounts, named people, named failures. Specificity is the fastest credibility proxy.
3. **Soft-CTA must name a real situation the reader already inhabits, not an aspiration.** "Save this before your next pipeline review / grant committee / trade" lands. "Save this to level up" gets ignored.

### Algorithm Constraints (2026)
- Replies weighted 13.5–27x > likes; bookmarks 10x.
- First 120 chars must clear the expand threshold; thread post #1 is scored independently.
- Links in main post → 30–90% reach penalty. Put in reply #1.
- 1–2 hashtags MAX. 3+ triggers spam suppression.
- Grok reads content semantically — no keyword tricks, no hashtag farming.
- Tue–Thu 9 AM–1 PM local; 3–5 posts/day spaced 2+ hours.
- Author replies in first 30 min boost reach (extends dwell + conversation depth).
- Native <60s video + images outperform text-only.

### AI-Slop Tells to Reject (2026)
- Decorative em dashes used mid-sentence ("the truth — it's complicated — is...")
- "It's not just X, it's Y"
- "Let me explain"
- "Imagine if…"
- Numbered lists with rocket emojis or sparkle emojis
- Generic openers: "Most people get this wrong", "Did you know…", "Here's a thread on…"
- False-vulnerability arcs: "I was wrong about X — here's what I learned" without specifics
- Aspirational CTAs: "save this to level up", "follow for more", "DM me for the guide"
- Em dash at the END of line 1 as a structural continuation IS fine (different from decorative mid-sentence use).

## 5. MARKET OVERLAYS

| Market | Overlay Rule |
|---|---|
| `b2b-saas` | Frame the insight as a **process failure**, never a talent/knowledge failure. B2B buyers have already spent on training; "your process is broken" is an invitation, "you didn't know X" is an insult. |
| `indie` | **Lead with the survivor, not the attempt count.** Indie audiences are allergic to "I tried 47 things" flexing — open with the one thing that lived, then earn the backstory. |
| `creator` | **Anchor the opener to an identity the reader is already quietly claiming** ("becoming X," not "being X"). The aspiration itself is the open wound. |
| `crypto` | **Never time-stamp the hook with a ticker or chain name.** Cycles rotate; "the market did this again" outlives "ETH is doing X" by months. |
| `science` | **Lead with the falsifiable surprise that overturns consensus.** Only in science is "the established view was wrong" an unambiguous trust-builder. |
| `generic` | Use only the universal rules; skip overlay. |

## 6. THREE VOICE MODES (Stage 1 picks one based on topic)

- **Wise Practitioner** — "I've done this, here's what I found." Lived experience, lesson as discovered truth. Default for case-study posts.
- **Contrarian Insider** — "Everyone says X. Here's why that's wrong." Scope to a belief the audience holds with *moderate* confidence — not a fringe position. Use sparingly.
- **Curious Explorer** — "I went down a rabbit hole on X and found something weird." Shares the *process* of learning. Drives bookmarks and replies more than shares.

## 7. EXAMPLE INVOCATIONS

```
/x-post-gen topic="indie product survival rates" market=indie format=single
/x-post-gen topic="why most SaaS demo requests die" market=b2b-saas format=single
/x-post-gen topic="why your niche isn't your topic" market=creator format=thread
/x-post-gen "post about prompt caching for Anthropic SDK"   # defaults to market=generic format=single
```

## 8. EXECUTION CHECKLIST
- [ ] User's topic, market, and format captured (default market=generic, format=single).
- [ ] Stage 1 produces an explicit BRIEF artifact before Stage 2 spawns.
- [ ] Stage 2 produces exactly 3 drafts with different vehicles.
- [ ] Stage 3 uses opus (depth of critique matters).
- [ ] Stage 4 applies only the prescribed fixes — does NOT regenerate.
- [ ] Final report shows each stage's output + the posting metadata.

## 9. ANTI-PATTERNS
- Skipping Stage 1 and going straight to drafts → produces voiceless posts that miss the gap.
- Letting Stage 2 self-critique → defeats the pipeline (the conflict-of-interest is real).
- Using sonnet for Stage 3 → critique becomes shallow; opus catches AI-slop tells reliably.
- Adding a "post it for me" stage → posting is a user-confirmed action, not a pipeline step.
- Generating 5+ drafts in Stage 2 → diminishing returns; 3 vehicles cover the design space.
- Forcing a thread when a single post would do (and vice versa).

## 10. OUTPUT TO USER
After the final stage, present:
- The final ready-to-post text in a code block.
- Character count.
- Posting metadata (window, media, first-reply plan, self-reply plan).
- The posting checklist.
- Briefly: which draft was chosen and what fixes were applied (one short paragraph).

Do not post anything. The user posts manually.
