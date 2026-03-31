---
tags: [certification, Anthropic, Claude, CCA-F, AI-architecture, roadmap]
date: 2026-03-31
---

> [!summary] **Claude Certified Architect – Foundations Roadmap **
> Comprehensive guide and 6-week roadmap for obtaining the **Claude Certified Architect – Foundations (CCA-F)** certification launched in March 2026. This note outlines exam logistics, domain weightings, required budget (~$25-$30), study resources, and essential architectural mental models for building production-grade agentic systems with Claude.

---

## 🏛️ Exam Overview & Eligibility

> [!info] The CCA-F Credential
> A ~301-level certification by Anthropic evaluating the ability to build reliable agentic loops, manage context, and make architectural tradeoffs using the Claude model family, API, Claude Code, and Model Context Protocol (MCP). Expects **6+ months** of hands-on experience.

* **Eligibility Constraint:** Currently strictly gated. Candidates **must be employed by a company in the Claude Partner Network** (membership is free to apply). 
* **Cost:** **$99** (Waived for the first 5,000 partner employees).
* **Format:** 120 minutes, 60 multiple-choice questions, online proctored.
* **Passing Score:** 720 (scale of 100–1,000).

---

## 🧠 Core Exam Domains

> [!info] Domain Weightings
> 1. **Agentic Architecture & Orchestration (27%):** Designing loops, subagent spawning, programmatic enforcement.
> 2. **Claude Code Configuration & Workflows (20%):** `CLAUDE.md` hierarchy, path scoping, CI/CD execution.
> 3. **Prompt Engineering & Structured Output (20%):** Few-shot prompting, forcing JSON, configuring `tool_use`.
> 4. **Tool Design & MCP Integration (18%):** Tool boundaries/descriptions for routing, MCP server/client builds.
> 5. **Context Management & Reliability (15%):** Mitigating "lost in the middle", handling context bloat, explicit memory passing.

### Critical Mental Models for the Exam
* **Agent Completion:** Rely strictly on the API's `stop_reason` field; **never** parse natural language to know when an agent is done.
* **Enforcing Rules:** Use programmatic hooks, prerequisite gates, and tool-call interceptors, **not** polite prompt requests.
* **Tool Selection:** Claude routes based on the tool's **description**, not just its function name.
* **Multi-Agent Memory:** Subagents do not inherit context. You must explicitly pass required memory/history.
* **Cost vs. Latency:** Use Batch API for asynchronous/overnight tasks; use Real-time API for blocking workflows.

---

## 💰 Budget & Tools Setup

> [!info] Estimated Study Budget: ~$25 - $30
> You do not need an enterprise license. A 30-day targeted sprint requires a minimal stack.

* **Claude Pro Subscription ($20/month):** Required to access the **Claude Code CLI** (essential for 20% of the exam). Includes ~44k tokens/5hr window.
* **Anthropic API Credits ($5 - $10):** Pay-as-you-go for building MCP servers/Python scripts. Use **Claude Haiku** ($1/$5 per M tokens) for cheap testing.
* **Free Tier Bonus ($5):** Available upon phone verification (Indian `+91` numbers are fully supported). 
	* *Warning:* Free credits expire **30 days** after claiming. Do not claim until ready to code. Paid credits expire after 12 months.

---

## 🗺️ 6-Week Study Roadmap

> [!info] Scenario-Based Testing
> Every CCA-F question is framed within one of 6 scenarios: Customer Support, Code Gen, Research, Dev Prod, CI/CD, or Structured Data. 

### Phase 1: Bridge the Gap to Claude (Weeks 1–2)
* Focus on the Messages API (`stop_reason`) and Claude-specific few-shot prompting.
* Master forcing structured JSON outputs and using nullable fields.

### Phase 2: Master Claude Code & Workflows (Week 3)
* Learn `CLAUDE.md` hierarchy (User vs. Project vs. Directory levels).
* Understand path-specific rules via YAML frontmatter (`paths: ["**/*.test.tsx"]`).
* Differentiate "Plan mode" (complex tasks) vs. "Direct execution" (known fixes).

### Phase 3: Advanced Architecture & Orchestration (Week 4)
* Focus on context isolation and explicitly passing memory to subagents.
* Practice building hub-and-spoke multi-agent systems.

### Phase 4: MCP and Tool Integration (Week 5)
* Write edge-case-proof tool descriptions.
* Practice truncating verbose tool outputs to prevent context bloat (mitigating the "Lost in the Middle" effect).

### Phase 5: The 6 Scenarios & Exam Simulation (Week 6)
* Drill Udemy practice exams.
* Analyze architectural tradeoffs (e.g., why programmatic solutions beat prompt-based solutions).

---

## 📚 References & Links

> [!bookmark] Resources
> - [Anthropic Console](https://console.anthropic.com) - Developer portal for API keys and claiming $5 free credits.
> - [Anthropic Academy (Skilljar)](https://academy.anthropic.com) - Official free courses: *Building with the Claude API*, *Claude Code in Action*, *Intro to MCP*.
> - **Official CCA-F Study Guide** - Downloadable from Anthropic; outlines the 6 production scenarios.
> - **Coursera:** *AI Automation with Claude* - Official foundational course.
> - **Udemy:** *CCA-F Practice Tests* - ~350 questions mapping to the 5 domains ($15-$35).
> - **Udemy:** *The Complete Course on Coding Agents* / *Claude Code Mastery* - Practical Python/MCP skill-building.

---

## ✅ Action Items

> [!todo] Next Steps
> - [ ] Verify company eligibility for the Claude Partner Network.
> - [ ] Create an Anthropic Console account and verify phone number (delay claiming the $5 credit until W1 of coding).
> - [ ] Subscribe to Claude Pro ($20) for 1 month to unlock the Claude Code CLI.
> - [ ] Complete the *Building with the Claude API* course on Anthropic Academy.
> - [ ] Purchase a highly-rated CCA-F Practice Test course on Udemy.
> - [ ] Build a local Python script forcing Claude to output strict JSON using the Messages API.

---

## ❓ Future Research

> [!question] Unresolved Queries
> - When exactly will Anthropic open the CCA-F exam to the general public (non-Partner Network)?
> - Are there any official Anthropic-authored practice exams available outside of third-party Udemy courses?
> - How strictly does the exam test specific `CLAUDE.md` YAML syntax versus general workflow concepts?