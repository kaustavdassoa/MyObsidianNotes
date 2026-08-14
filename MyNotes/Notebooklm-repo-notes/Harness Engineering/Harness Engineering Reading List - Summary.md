
# My AI Adoption Journey By Mitchell Hashimoto

### **Introduction: The Phases of Tool Adoption**

Mitchell Hashimoto built HashiCorp and co-created Terraform. In February 2026, he published a [blog post](https://mitchellh.com/writing/my-ai-adoption-journey) describing a habit he’d developed while working with AI agents: every time an agent made a mistake, he engineered a permanent fix into the agent’s environment. He called it “**engineering the harness**.”

Hashimoto notes that adopting any meaningful tool requires pushing through three phases: **inefficiency**, **adequacy**, and eventually, **workflow/life-altering discovery**. He initially faced friction and skepticism with AI but forced himself through the early phases to become a more well-rounded software craftsman.

### **Step 1: Drop the Chatbot**

- **The Problem:** Chat interfaces (like ChatGPT or web Gemini) are highly inefficient for coding. You spend too much time hoping the AI gets it right and manually correcting it when it fails, leading to frustrating copy-pasting loops.

- **The Solution:** You must switch to using an **agent**—an LLM equipped with tools that allow it to read files, execute programs, and make HTTP requests in a loop.

### **Step 2: Reproduce Your Own Work**

- **The Strategy:** To force himself to learn, Hashimoto started doing his work twice: once manually, and then again by fighting an agent (like Claude Code) to produce the exact same result.

- **The Lessons Learned:**  
    - Do not ask the AI to do everything at once ("draw the owl"). Break tasks down into clear, actionable steps.
    - Separate planning sessions from execution sessions.
    - Give agents a way to verify their own work to prevent regressions.
- **The Result:** He learned the edges of what agents can and cannot do. Knowing _when not to use_ an agent saved massive amounts of time, bringing him to a baseline where using AI was no slower than working manually.
### **Step 3: End-of-Day Agents**

- **The Strategy:** He began blocking out the last 30 minutes of his workday to kick off agents that would run while he was offline.
- **Best Use Cases:**
    - **Deep Research:** Surveying libraries, compiling pros/cons, and summarizing social sentiment.
    - **Exploration:** Having parallel agents attempt vague ideas he didn't have time to start himself.
    - **Triage:** Scripting agents via the GitHub CLI to review issues and pull requests to highlight high-value tasks.
- **The Result:** This provided a "warm start" the following morning, making him slightly more productive than he was prior to using AI.
### **Step 4: Outsource the "Slam Dunks"**

- **The Strategy:** Once he knew exactly what tasks the AI was great at, he started outsourcing those highly confident tasks to a background agent while he focused on complex, enjoyable, deep-thinking coding tasks.
- **Crucial Rule:** **Turn off agent notifications.** Context switching is too expensive. The human must dictate when to check on the agent (during natural breaks), not the other way around.
- **Skill Retention:** Working on hard problems manually while outsourcing the easy ones helped counteract the fear that AI degrades a developer's skills.
### **Step 5: Engineer the Harness ("Harness Engineering")**

- **The Strategy:** To ensure agents produce the right result on the first try, you must give them tools to realize when they are wrong. Whenever an agent makes a mistake, engineer a permanent fix.
- **How to do it:**
    - **Implicit Prompting:** Create an `AGENTS.md` file in the repository outlining bad behaviors to avoid and the correct APIs/commands to use.
    - **Programmed Tools:** Write dedicated scripts (e.g., filtered tests, screenshot generators) that the agent can run to verify its own work.
### **Step 6: Always Have an Agent Running**
- **The Goal:** Hashimoto is currently striving to always have an agent running in the background. If one isn't running, he asks himself what tasks he could delegate.
- **Current State:** He pairs this philosophy with slower, highly thoughtful models (like "Amp's deep mode" / GPT-5.2-Codex), which might take 30+ minutes but produce excellent results. He currently achieves this for about 10-20% of his workday.
### **Conclusion & Final Thoughts**

- Hashimoto has reached a point where AI tooling provides genuine, life-altering efficiency gains, allowing him to focus on the work he loves while robots handle the tedious tasks.
- He emphasizes that he has no financial ties to any AI company—he is just a software craftsman sharing his workflow.
- While he finds massive value in AI for himself, he holds a measured view, fully respecting developers who choose not to use AI, and expresses a valid concern about how AI might negatively impact the skill formation of junior developers who lack a strong grasp of fundamentals.


# Harness Engineering: The Execution Layer AI Agents Actually Need

### **What is Harness Engineering?**

The term "Harness Engineering" was popularized after Mitchell Hashimoto and companies like OpenAI and Anthropic described a crucial layer of AI agent development. It refers to designing the execution environment around an autonomous AI agent—defining the tools it can use, where it gets information, how it checks its work, and constraints to prevent drift.

It fits into a three-layer progression of AI development:

1. **Prompt Engineering:** Optimizes a single conversation turn/output.
2. **Context Engineering:** Manages what the model sees (what fits in the context window).
3. **Harness Engineering:** Builds the world the agent operates in, allowing it to run reliably for hours without human supervision.
### **Lessons from OpenAI’s "Million-Line Codebase" Experiment**

OpenAI successfully used an AI coding agent (Codex) to generate a million lines of production code over five months without humans writing a single line. In doing so, they solved four major harness problems:

- **Problem 1: No Shared Understanding.** A single massive instruction file (`AGENTS.md`) failed because of context bloat and rapid documentation rot.
    - _The Fix:_ Shrink `AGENTS.md` to just a "map" that points to structured documentation. If rules aren't in context at runtime, they don't exist.
- **Problem 2: QA Couldn't Keep Pace.** Human reviewers couldn't keep up with 6-hour agent coding marathons.
    - _The Fix:_ Agents were plugged into Chrome DevTools to observe runtime events, run log queries (LogQL), and measure specific thresholds (e.g., verifying a service starts in under 800ms).
- **Problem 3: Architectural Drift.** Unchecked, the agent copied whatever patterns it found, including bad ones.
    - _The Fix:_ Strict, one-way dependency layers were enforced using custom linters that provided inline fix instructions directly to the agent.
- **Problem 4: Silent Technical Debt.**
    - _The Fix:_ Core principles were encoded into the repository, and background tasks ran continuously to submit small, automated refactoring pull requests.
        
### **Why Agents Can’t Grade Their Own Work**

Agents face specific failure modes when left to evaluate themselves:

- **Context Anxiety:** As models sense their context window filling up, they take shortcuts and wrap up tasks prematurely. (Fix: cap the tokens below the actual limit to trick the model into thinking it has ample runway, or use context resets/compaction).

- **Self-Evaluation Bias:** Agents tend to score their own work highly and ignore glaring errors.

    - _The Fix (Anthropic's approach):_ Separate the "**Generator**" and "**Evaluator**" completely, similar to a GAN (Generative Adversarial Network). Anthropic used a **Planner**, a **Generator**, and an **independent Evaluator** (using Playwright for actual browser testing). While this three-agent harness cost 20x more than a solo agent, it was the difference between a broken product and a fully functional one.
### **The Retrieval Problem (And Milvus's Solution)**

An agent harness heavily relies on retrieving the right knowledge at the right time. Agents need to execute two types of searches simultaneously:

1. **Semantic Search:** Understanding concepts (e.g., matching "session management" with "user authentication"). Requires vector embeddings.

2. **Exact Match:** Finding arbitrary strings like a function name (`validateToken`). Requires traditional keyword matching (BM25).

Previously, this required running two separate index systems that constantly risked falling out of sync.

- **The Milvus 2.6 Fix:** Milvus introduced a single hybrid pipeline combining dense embeddings (for semantics) and Sparse-BM25 (for exact match). It updates global IDF statistics automatically as docs change and merges the ranked results internally. This single-index, single-interface system is critical for living, breathing agent repositories.
### **The Ultimate Goal: Deleting the Harness**

The post concludes with a fundamental philosophy: the best harness components are designed to be deleted.

Every part of a harness (like sprint decomposition or context tricks) is built to compensate for a current model limitation. As underlying LLMs improve in context stamina and logic, these components should become redundant. The goal of Harness Engineering is to constantly recalibrate, removing overhead as models get smarter.