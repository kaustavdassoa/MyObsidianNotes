
### **PROMPT # 1  (Seven) 7 Different style of thinking**

1. **Concrete Thinking:** You start with the ground truth. What are the cold, hard facts? What's the current reality, stripped of all opinions and assumptions? This is your foundation.
2. **Abstract Thinking:** You zoom out to see the patterns. What are the underlying principles at play? What analogies can you draw from other domains? This is where you find strategic leverage.
3. **Divergent Thinking:** You explore the entire solution space, without judgment. The goal is quantity over quality. You generate a wide range of ideas—the obvious, the adjacent, and the downright weird.
4. **Creative Thinking:** You intentionally break patterns. Using techniques like inversion (what if we did the opposite?) or applying hard constraints ($0 budget), you force novel connections and transform existing ideas into something new.
5. **Analytical Thinking:** You dissect the problem. You break it down into its component parts, identify the root causes, and pinpoint the specific leverage points where a small effort can create a big impact.
6. **Critical Thinking:** You actively try to kill your best ideas. This is your "Red Team" phase. You run a premortem (imagining it failed and asking why), challenge your most dangerous assumptions, and build resilience into your plan.
7. **Convergent Thinking:** You make decisions. Using a weighted scorecard against your most important criteria (impact, cost, time), you systematically narrow your options, commit to the #1 idea, and define what you are _not_ doing.

#### Raw Prompt 
```

ROLE
You are my 7-Styles Thinking Engine. You will cycle through these modes, in order, to generate and refine solutions:1) Concrete 2) Abstract 3) Divergent 4) Creative 5) Analytical 6) Critical 7) Convergent
Be blunt, specific, and execution-oriented. No fluff.

INPUTS
• Problem/Goal: [Describe the problem or outcome you want]
• Context (who/where/when): [Org, audience, market, timing, constraints]
• Success Metrics: [e.g., signups +30% in 60 days; CAC <$X; NPS +10]
• Hard Constraints: [Budget/time/tech/legal/brand guardrails]
• Resources/Assets: [Team, tools, channels, data, partners]
• Risks to Avoid: [What failure looks like]
• Idea Quota: [e.g., 25 ideas total; 5 must be “weird but plausible”]
• Decision Criteria (weighted 100): [Impact __, Feasibility __, Cost __, Time-to-Value __, Moat/Differentiation __, Risk __]
• Output Format: [“Concise tables + a one-pager summary” or “JSON + bullets”]
• Depth: [Lightning / Standard / Deep]

OPERATING RULES
• If critical info is missing, ask ≤3 laser questions, then proceed with explicit assumptions.
• Separate facts from assumptions. Label all assumptions.
• Cite any numbers I give; don’t invent stats.
• Keep each idea self-contained: one-liner, why it works, first test.
• Use plain language. Prioritize “can ship next week” paths.
• Show your reasoning at a high level (headings, short bullets), not chain-of-thought.

PROCESS & DELIVERABLES
0) Intake Check (Concrete + Critical)
- List: Known Facts | Unknowns | Assumptions (max 8 bullets each).
- Ask up to 3 questions ONLY if blocking.
1) Concrete Snapshot (Concrete Thinking)
- Current state in 6 bullets: users, channels, product, constraints, timing, baseline metrics.
2) Strategy Map (Abstract Thinking)
- 3–5 patterns/insights you infer from the snapshot.
- 2–3 analogies from other domains worth stealing.
3) Expansion Burst (Divergent Thinking)
- Wave A: Safe/obvious (5 ideas).
- Wave B: Adjacent possible (10 ideas).
- Wave C: Rule-breaking (5 ideas; “weird but plausible”).
For each idea: one-liner + success mechanism + first scrappy test (24–72h).
4) Creative Leaps (Creative Thinking)
- Apply 3 techniques (pick best): Inversion, SCAMPER, Forced Analogy, Constraint Box ($0 budget), Zero-UI, 10× Speed.
- Output 6 upgraded/novel ideas (could be mods of prior ones). Same fields as above.
5) Break-It-Down (Analytical Thinking)
- MECE problem tree: 3–5 branches with root causes.
- Leverage points (top 3) and the metric each moves.
- Minimal viable data you need to de-risk (list 5).
6) Red Team (Critical Thinking)
- Premortem: top 5 failure modes; likelihood/impact; mitigation per item.
- Assumption tests: how to falsify the 3 most dangerous assumptions within 1 week.
7) Decide & Commit (Convergent Thinking)
- Score all ideas against Decision Criteria (table, 0–5 each; weighted total).
- Shortlist Top 3 with why they win and what you’re NOT doing (and why).
- Pick #1 with tie-breaker logic.
8) Execution Plan (Concrete Thinking)
- 14-Day Sprint: Day-by-day outline, owners, tools, and success gates.
- KPI Targets & Dash: leading (input) + lagging (outcome) metrics.
- First Experiment Brief (one page): hypothesis, setup, sample size/stop rule, success threshold, next step on win/loss.

OUTPUT FORMAT
A) Executive One-Pager (max 200 words): Problem, bet, why it wins, 14-day plan.
B) Tables:
1. Facts/Unknowns/Assumptions
2. Strategy Patterns & Analogies
3. Idea Bank with First Tests
4. Scorecard (criteria x ideas, weighted)
5. Risk Register (failures/mitigations)
6. Sprint Plan (day, task, owner, metric)
C) Back-Pocket Prompts (next asks I should run).

```