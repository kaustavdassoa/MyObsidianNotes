# 📚 Intermediate Engineering Study Guide: GitHub Copilot Extensibility & Code Migration Pipelines

## 1. Concise Overview
To build functional, automated code-migration pipelines using GitHub Copilot, you must move beyond basic code completions and leverage its layered customization architecture. This architecture consists of Instructions, Skills, Agents, Hooks, and the Model Context Protocol (MCP). 

For an intermediate engineer, the goal is to master the programmatic orchestration of these components. By correctly scoping `.github/copilot-instructions.md` for baseline standards, utilizing `applyTo` globs for domain-specific rules, structuring `SKILL.md` files to exploit progressive loading, and deploying sub-agents in automated review loops, you can ensure AI-generated migrations align perfectly with your existing DevOps scaffolding while strictly managing token consumption.

---

## 2. Key Concepts and Definitions

*   **Instructions:** The foundational layer establishing *"who you are"*. They are passive markdown files setting coding standards and architectural decisions. They load automatically.
*   **Agent Skills:** The capability layer establishing *"how to do it"*. These are self-contained folders with a `SKILL.md` file, packaged with scripts and references. They load on-demand based on their YAML description to save tokens.
*   **Agents:** The specialized roles defining *"who does it"*. Configured via `.agent.md`, they bundle specific tools and models, and orchestrate complex tasks either via user-driven **Handoffs** or autonomous **Sub-agents**.
*   **Hooks:** The safety layer ensuring *"it's done safely"*. These are deterministic code scripts (not LLM prompts) that intercept the agent lifecycle (e.g., `PreToolUse` or `PostToolUse`) to validate parameters or run linters.
*   **Model Context Protocol (MCP):** An open standard that connects Copilot to external tools, APIs, and datasets. *Note: While the sources explain MCP's usage (e.g., connecting to OneDrive, Teams, or custom .NET/Python SDKs), the underlying raw protocol code specification is not detailed in the provided texts.*
*   **Progressive Disclosure:** A token-saving loading model used by Skills, where metadata is always loaded, but the body and references are only injected into the context window when Copilot determines the skill is relevant.
*   **Copilot Coding Agent:** An asynchronous software engineering agent that runs inside GitHub Actions sandboxes to independently tackle assigned tasks (like PR creation and testing).

---

## 3. Engineering a Code-Migration Pipeline: Rules & Examples

To build a code-migration pipeline (e.g., migrating React 18 to React 19, or Oracle to PostgreSQL), you must distribute logic across the architecture to optimize tokens and ensure reliability.

### A. Structuring `copilot-instructions.md`
**Rule:** Never put workflow orchestration or detailed migration runbooks in `copilot-instructions.md`. 
*   **Why:** This file is "always-on" and is injected into every single interaction. Overloading it wastes 200–500 tokens per request and confuses the AI due to non-determinism.
*   **What to include:** Keep it to 5–15 core rules. Define the elevator pitch, global tech stack, project structure, and global DevOps testing standards.

### B. Path-Specific Instructions (`*.instructions.md`)
**Rule:** Use YAML frontmatter `applyTo` globs to enforce domain-specific rules.
For a migration, you want the AI to only apply new framework rules when touching specific files.
```yaml
---
description: 'React 19 concurrent patterns'
applyTo: ['src/components/**/*.tsx']
---
# Use useTransition and useOptimistic instead of legacy state...
```

### C. Drafting Effective `SKILL.md` Runbooks
**Rule:** Package repeatable migration workflows as Agent Skills using the **Progressive Disclosure** model to minimize prompt tokens.
*   **Tier 1 (Metadata):** The YAML frontmatter (`name` and `description`). This costs ~50-100 tokens and is always in context. Make the description highly keyword-rich (e.g., "Use when converting Oracle stored procedures to PostgreSQL") so Copilot knows when to trigger it.
*   **Tier 2 (Body):** Keep the `SKILL.md` body under 500 tokens. Outline the high-level steps (e.g., "1. Analyze syntax, 2. Preserve signatures, 3. Apply COLLATE 'C'").
*   **Tier 3 (References):** Put heavy code templates, testing scaffolding, and detailed API mappings in the `references/` folder (keep under 1,000 tokens/file). These are only loaded when explicitly referenced by the skill.

### D. Agent Orchestration: Sub-agent Review Loops
**Rule:** For automated migrations, use **Sub-agents** (programmatic delegation) rather than **Handoffs** (user-driven relay races). 
*   To align generated code flawlessly with DevOps frameworks, you can build an **automated review loop**. 
*   A parent agent (e.g., `MigrationImplementer`) generates the code, then autonomously invokes a sub-agent (e.g., `CodeReviewer` with access to DevOps linters/tests). The sub-agent reviews the code in an isolated context and returns feedback. The parent iterates until the tests pass.
*   *Constraint:* Sub-agents cannot invoke other sub-agents (single-level depth only) to prevent infinite loops.

### E. Ensuring Alignment with DevOps via Hooks
**Rule:** Enforce non-negotiable DevOps scaffolding using Hooks.
*   Hooks consume **0 tokens** because they bypass the LLM and execute deterministically. 
*   Use a `PostToolUse` hook to automatically run your pre-existing CI/CD linters or tests after the agent edits a file. If the test fails, the agent is notified to fix it.

---

## 4. Token Cost Strategy (The "Why")

Token economics dictate where configuration lives.

| Component | Loading Mechanism | Token Cost | Optimization Guidance |
| :--- | :--- | :--- | :--- |
| `copilot-instructions.md` | Always-on | 200–500 tokens every time | Limit to 5-15 global rules. |
| `.instructions.md` | Conditional (`applyTo`) | 100–300 tokens per file | Split by language/framework. |
| Skill Metadata | Always-on | ~50–100 tokens per skill | Write highly precise descriptions. |
| Skill Body / References | On-demand | 500 / 1000 tokens | Move detailed procedures to `references/`. |
| **Hooks** | Event-triggered | **0 tokens (code execution)** | Use for strict DevOps enforcement. |

---

## 5. Connections Across Sources

*   **Customization vs. Automation:** Source 1 and Source 3 explain how instructions provide *context*, but Source 4 and Source 5 show that to automate a multi-step migration, you must graduate from passive instructions to active **Agent Skills**, which bundle scripts and references.
*   **Local IDE vs. Background CI/CD:** Source 2 demonstrates building an agent in VS Code using MCP for local execution. However, Source 6 bridges this to DevOps by introducing the **Copilot Coding Agent**, which runs these exact agentic workflows asynchronously in GitHub Actions sandboxes, handling the git branching, committing, and PR creation.
*   **Real-world Migration Patterns:** Source 10 provides concrete community examples of how this is applied (e.g., Oracle to Postgres migration skills, Pester migration, React legacy context migrations), proving that progressive disclosure and bundled references are industry standard for complex tech-debt burn-down.

---

## 6. Summary of Sources

1.  **5 tips for writing better custom instructions:** Advises keeping `copilot-instructions.md` focused on project overviews, tech stacks, and global guidelines.
2.  **Build NEXT-LEVEL Copilot Agents (YouTube):** Demonstrates importing a Copilot Studio agent into VS Code, connecting MCP servers (OneDrive, Teams), and orchestrating multi-step plans (`/plan`, `/init`).
3.  **Defining Custom Instructions:** Explains YAML frontmatter, `applyTo` globs, and formatting rules to dynamically apply instructions to specific file types.
4.  **GitHub Copilot Customization Architecture (Gist):** A Principal Engineer's guide covering the 5 architecture layers. Details the critical distinction between Handoffs and Sub-agents, progressive disclosure in Skills, and the Token Cost Strategy.
5.  **GitHub Copilot Skills (DevOps/SREs):** Highlights how Agent Skills package multi-step procedures and bundled assets for repeatable workflows like CI/CD triage and Terraform reviews.
6.  **GitHub Copilot coding agent 101:** Explores the asynchronous coding agent that lives in GitHub Actions, opening PRs and iterating based on human reviews.
7.  **Use custom instructions in VS Code:** Documentation on file generation (`/create-instruction`), prioritization (Personal > Repo > Org), and syncing configurations.
8.  **What are Agents, Skills, and Instructions:** A high-level differentiation: Instructions (long-lived guardrails), Skills (on-demand workflows), Agents (opinionated personas).
9.  **Your first custom instructions:** Quick-start examples for modifying how Copilot generates functions.
10. **awesome-copilot/README.skills.md:** A vast directory of community skills, showcasing advanced migration runbooks (Oracle, React 18/19, .NET upgrades) utilizing bundled `references/` folders.

---

## 7. Practice Questions & Answers

**Q1: You are building a pipeline to migrate legacy React class components to React 19. Where should you store the complex before-and-after code mapping templates?**
> **Answer:** In a `references/` folder inside an Agent Skill directory (e.g., `.github/skills/react19-migration/references/`). This utilizes progressive disclosure, meaning the heavy templates are only loaded into the token context when the skill is explicitly invoked or semantically triggered.

**Q2: What is the primary architectural difference between an Agent Handoff and a Sub-agent in Copilot?**
> **Answer:** A Handoff is a *user-driven* relay race where the human must click a button to pass shared context to the next agent. A Sub-agent is *agent-driven* programmatic delegation where the parent agent autonomously spins up an isolated worker in the background (e.g., for automated review loops).

**Q3: How deep can a Sub-agent orchestration tree go? Can your `MigrationImplementer` call a `CodeReviewer` sub-agent, which then calls a `SecurityScanner` sub-agent?**
> **Answer:** No. Sub-agents have a single-level depth limit. They cannot invoke other sub-agents. The top-level parent agent must coordinate and invoke both the `CodeReviewer` and `SecurityScanner` separately.

**Q4: How do you ensure an instruction only applies to backend testing files?**
> **Answer:** Create a `.instructions.md` file and use the YAML frontmatter property: `applyTo: '**/*.test.ts'` (or relevant pattern) so it conditionally loads only when those files are in context.

**Q5: Why is placing a multi-stage migration runbook inside `copilot-instructions.md` considered a major anti-pattern?**
> **Answer:** Because `copilot-instructions.md` is "always-on". Putting a runbook there violates separation of concerns, causes unreliable execution due to LLM non-determinism, and wastes 200-500+ tokens on every single chat interaction, even when the user isn't doing a migration.

---

## 8. Final Revision Checklist (Exam-Style Points)

*   [ ] **Token Priority:** Know that `copilot-instructions.md` is always loaded (highest token waste if abused), Skills are progressively loaded (highly efficient), and Hooks execute as code (0 tokens).
*   [ ] **Scoping (YAML):** Remember the `applyTo:` array/string syntax in `.instructions.md` to map guidelines to specific repo paths.
*   [ ] **Skill Anatomy:** A `SKILL.md` requires `name` and `description` frontmatter. Copilot uses the *description* to automatically discover and load the skill.
*   [ ] **Sub-agent Limits:** Memorize the "single-level depth" rule for sub-agents (no infinite nesting) and the fact they operate in an *isolated context* from the parent.
*   [ ] **Coding Agent Sandbox:** Understand that the autonomous "coding agent" executes inside GitHub Actions, not locally, and requires human PR approval before merging.
*   [ ] **Hooks for DevOps:** Use `PreToolUse` and `PostToolUse` hooks to deterministically trigger linters, block dangerous commands, or integrate seamlessly with DevOps scaffolding without relying on AI prompt obedience.