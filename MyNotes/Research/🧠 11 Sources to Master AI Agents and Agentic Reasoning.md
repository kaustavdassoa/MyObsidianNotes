Welcome to your structured knowledge base for Agentic AI. This document is formatted for Obsidian to help you link, tag, and explore the cutting edge of AI agent architectures, capabilities, and security.

---

## 🌐 Google Open Knowledge Format (OKF) & Data Sharing

Before diving into the papers, it is essential to consider the **Google Open Knowledge Format (OKF)**, recently introduced by Google Cloud Data Analytics. OKF formalizes the LLM-wiki pattern into a portable, vendor-neutral format representing knowledge as a directory of Markdown files with YAML frontmatter.

**Why it matters for AI Agents:**

Instead of models repetitively searching silos for facts, teams can provide agents a shared, evolving Markdown library. OKF allows knowledge to be produced by humans or data pipelines and consumed directly by AI agents without specialized SDKs or integrations. This solves the "knowledge silo" problem in agentic workflows.

- 🔗 **Read more:** [How the Open Knowledge Format can improve data sharing](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)

---

## 📚 Essential Paper & Guide Directory

| Name of the topic | Brief description of the topic | Direct URL |
| :--- | :--- | :--- |
| **Agentic Reasoning for LLMs** | Explores how reasoning shifts from passive thinking to active environment interaction, categorizing foundations like planning, tool use, and search in stable environments. | [Read Paper](https://arxiv.org/abs/2601.12538) |
| **Agent Skills for LLMs: Architecture, Acquisition, Security** | Analyzes the architecture of AI agent skills, how they are acquired, and the underlying security implications for LLMs. | [Read Paper](https://hatohato.jp/ai/papers/20260622_agent_skills_llm.html) |
| **The Landscape of Agentic Reinforcement Learning for LLMs** | Reframes LLMs from sequence generators to autonomous agents using Agentic RL and POMDPs, focusing on adaptive behaviors like memory and self-improvement. | [Read Paper](https://arxiv.org/abs/2509.02547) |
| **Toward Efficient Agents: Memory, Tool learning, and Planning** | Investigates the efficiency of agents by minimizing resource consumption (tokens, latency) while maintaining performance across memory, tools, and planning. | [Read Paper](https://arxiv.org/abs/2601.14192) |
| **Memory for Autonomous LLM Agents** | Details how memory transforms stateless LLMs into self-evolving agents, addressing challenges like context compression, parametric vs. external storage, and "summarization drift." | [Read Paper](https://arxiv.org/abs/2603.07670) |
| **A practical guide to building agents (by OpenAI)** | An engineering-focused playbook covering agent design foundations (models, tools, instructions), orchestration patterns, and critical guardrails for production. | [Read Guide](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/) |
| **AI Agent Systems: Architectures, Applications, and Evaluation** | Synthesizes agent architectures, design trade-offs (latency vs. accuracy), and evaluation complexities, organizing work into a unified taxonomy. | [Read Paper](https://arxiv.org/abs/2601.01743) |
| **Making Sense of AI Agents Hype** | Reviews practitioner experiences and architectural strategies transitioning from pilots to production, highlighting the need for robust control planes and integration. | [Read Paper](https://arxiv.org/abs/2604.00189) |
| **Agent-as-a-Judge** | Introduces a framework where agentic systems evaluate other agentic systems, providing step-by-step intermediate feedback rather than just assessing final outcomes. | [Read Paper](https://openreview.net/forum?id=DeVm3YUnpj) |
| **Towards Trustworthy Agentic AI** | Comprehensive survey on the safety, robustness, privacy, and system security of agents, detailing the "lethal trifecta" of AI vulnerabilities. | [Read Paper](https://arxiv.org/abs/2605.23989) |
| **The Attack and Defense Landscape of Agentic AI** | Examines the security landscape from a systems perspective, mapping heterogeneous untrusted interfaces and unconstrained data flow vulnerabilities. | [Read Paper](https://arxiv.org/abs/2603.11088) |

---
*Tags:* #AI #AgenticAI #LLM #ReinforcementLearning #MachineLearning #Cybersecurity #Obsidian