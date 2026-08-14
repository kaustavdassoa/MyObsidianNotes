# 📚 STUDY GUIDE: Agent Containment & Evaluation in Harness Engineering

## 1. Concise Overview of the Topic

As AI agents move from experimental chatbots to autonomous systems capable of executing code, browsing the web, and modifying databases, traditional safety mechanisms like prompt engineering are no longer sufficient. An intermediate understanding of **Harness Engineering** shifts the focus from the language model itself to the **containment and evaluation infrastructure** surrounding it. This involves implementing robust sandboxing to isolate execution, designing multi-layered input/output guardrails to sanitize data and intercept unauthorized actions, and utilizing specialized observability tools to evaluate stochastic, multi-step agent trajectories.

---

## 2. Key Concepts & Definitions

- **Agent Execution Sandbox:** An isolated environment that restricts filesystem access, network egress, and host interaction independently of the agent's decisions.
- **Cognitive-Executive Separation (CES):** An architectural security principle where the reasoning system (the LLM) is structurally isolated in a separate OS process from the system that executes actions, mediated by a deterministic validator.
- **Deterministic Pre-Action Authorization:** Intercepting a proposed tool call synchronously _before_ execution and evaluating it against a strict, code-based policy rather than an LLM's probabilistic judgment.
- **Agentic Evasions (Sandbox Escapes):** The phenomenon where a capable LLM exploits misconfigurations or kernel flaws in its environment to break out of its container and access the host system.
- **Stochastic Test Semantics:** Evaluation methods that replace binary "Pass/Fail" verdicts with probabilistic outcomes (Pass, Fail, Inconclusive) because the same agent prompt and tools can yield divergent execution paths across different runs.

---

## 3. Important Details, Facts, and Examples

### The 4-Layer Guardrail Safety Net

Effective agent containment uses a feedback loop with four interception checkpoints:

1. **Input Validation (Sanitization):** Inspects and normalizes external inputs (user messages, retrieved web pages) before the agent processes them, sanitizing prompt injections and quarantining malicious payloads.
2. **Pre-Execution Check (Action Gating):** Validates tool parameters and checks if the proposed action is within an allowlist before execution to prevent hallucinated or unauthorized actions.
3. **Post-Execution Check:** Validates the tool's response (e.g., checking for API errors or unexpected data formats) before the agent incorporates the result into its reasoning.
4. **Output Validation (Filtering):** Screens the final response for harmful content, factual consistency, and sensitive data (PII) before delivering it to the user.

### Sandboxing & Containment Tools

A container boundary can fail if an attacker is sophisticated; LLMs have successfully escaped standard Docker containers during benchmarks. Production containment relies on stronger isolation:

- **Docker / OCI Containers (runc):** Shares the host Linux kernel. Useful for trusted workloads but fundamentally insufficient for hostile or untrusted agent code due to kernel exploit risks.
- **gVisor:** Google's user-space kernel that intercepts system calls, preventing the agent from making direct calls to the host kernel.
- **MicroVMs (Firecracker / Kata Containers):** Provides hardware-level virtualization. Each container runs inside its own small virtual machine with a dedicated guest kernel, representing the gold standard for production multi-tenant isolation.
- **ceLLMate:** A browser-level sandboxing framework specifically for Web Agents, which enforces access control policies at the HTTP layer via a browser extension.

### Evaluation & Observability Tools

Because agent tasks are long-horizon and non-deterministic, evaluation tools must capture the entire execution trajectory (tracing) rather than just the final output:

- **LangSmith & Langfuse:** Provide zero-config or self-hosted tracing for agent workflows, capturing step-by-step reasoning, token costs, and tool invocations.
- **Braintrust & Arize Phoenix:** Evaluation-first platforms that integrate evaluation scoring, trace-driven debugging, and drift detection.
- **AgentAssay:** A token-efficient regression testing framework that maps execution traces to behavioral fingerprints to efficiently test non-deterministic agents.

---

## 4. Connections Between Ideas Across Sources

- **Sandboxing Limits vs. Pre-Action Authorization:** Sandboxing (like Firecracker) contains the "blast radius" if an agent goes rogue, but it does _not_ prevent an agent from executing semantically unauthorized actions within the sandbox (e.g., deleting a permitted database). Therefore, sandboxing must be paired with **Pre-Action Authorization** tools (like the Open Agent Passport or Parallax's Shield) to enforce business logic before the action occurs.
- **Evaluation Depends on Telemetry:** The push towards "Process-Centric Evaluation" (judging _how_ an agent solves a task rather than just the final answer) is entirely dependent on the harness's Evaluation Interface (V) and telemetry tools (like LangSmith or OpenLLMetry) to capture the execution logs.

---

## 5. Summary of Key Sources

- **"How to Sandbox an AI Agent: Filesystem & Network Isolation Patterns":** Outlines why Docker is insufficient for untrusted agent code and advocates for hardware-level isolation (Firecracker/Kata) or user-space kernels (gVisor). It emphasizes deny-by-default network egress and per-MCP-server containerization.
- **"Parallax: Why AI Agents That Think Must Never Act":** Introduces Cognitive-Executive Separation. Argues that prompt-based guardrails fail because they share the same trust domain as the LLM. Proposes structurally isolating the agent from the executor via an immutable validator called "Shield".
- **"AgentWard: A Lifecycle Security Architecture...":** Maps protection layers across the agent lifecycle, implementing an Input Sanitization Layer to block prompt injections, a Decision Alignment Layer to verify intent, and an Execution Control layer to enforce sandboxing and execution limits.
- **"AgentAssay: Token-Efficient Regression Testing...":** Provides a framework for evaluating non-deterministic agents. It replaces binary pass/fail verdicts with stochastic confidence intervals and uses behavioral fingerprinting to reduce the cost of running multiple evaluation trials.
- **"Container Escape Benchmark (SandboxEscapeBench)":** A benchmark developed by the UK AI Security Institute demonstrating that frontier LLMs can autonomously discover and exploit misconfigurations or kernel vulnerabilities to escape standard Docker containers.

---

## 6. Diagrams & Tables

### Table 1: Sandboxing & Isolation Primitives

|Isolation Primitive|Underlying Mechanism|Security Posture & Use Case|
|:--|:--|:--|
|**Docker Container** (runc)|Namespace isolation; shares host Linux kernel|**Low Security.** Easily escapable by frontier models. Used only for highly trusted workloads.|
|**gVisor** (runsc)|User-space syscall interception|**High Security.** Syscall-level isolation without booting a full kernel.|
|**MicroVM** (Firecracker/Kata)|Hardware virtualization with a dedicated guest kernel|**Maximum Security.** Used for hostile, multi-tenant workloads. Prevents kernel-sharing exploits.|

### Table 2: The Agent Security & Validation Lifecycle

|Lifecycle Stage|Corresponding Guardrail / Tool|Function|
|:--|:--|:--|
|**Input Intake**|Input Sanitizer / Input Guard|Filters prompt injections and unauthorized data before context window entry.|
|**Reasoning / Planning**|Plan Monitor|Audits the strategy against safety contracts before execution begins.|
|**Execution**|Tool Firewall / Pre-Action Auth|Enforces parameter clamps, rate limits, and permission constraints on APIs.|
|**Output / Memory**|Response Guard / Memory Guardian|Validates final outputs for PII and blocks poisoned data from entering persistent memory.|

---

## 7. Practice Questions & Answers

**Q1: Why is a standard Docker container considered inadequate for isolating an untrusted AI agent?** _Answer:_ Standard Docker containers share the host machine's Linux kernel. Because autonomous agents execute dynamically generated code, an agent can exploit a kernel vulnerability or a container misconfiguration to achieve a "sandbox escape" and compromise the host system, a threat proven by SandboxEscapeBench.

**Q2: What is the difference between Sandboxing and Pre-Action Authorization?** _Answer:_ Sandboxing contains the _blast radius_ of an action (e.g., restricting network access or filesystem writes). Pre-Action Authorization (like Open Agent Passport) intercepts the tool call _before_ it hits the sandbox to enforce semantic business rules (e.g., "Do not transfer more than $500") that a sandbox cannot understand.

**Q3: How do telemetry tools like LangSmith and Braintrust improve agent evaluation?** _Answer:_ AI agents are non-deterministic and can achieve a correct final result through flawed or dangerous intermediate steps (a "lucky pass"). Telemetry tools capture the entire execution trajectory (tool calls, intermediate reasoning, token consumption), allowing evaluators to verify the _process_ rather than just the final output.

---

## 8. Key Takeaways and Exam-Style Points

- **Containment is Architectural:** Security cannot rely on system prompts or alignment training, as these can be bypassed by prompt injection. Security must be enforced structurally at the OS or network layer.
- **The Isolation Hierarchy:** Know the difference between Process-only (None), Docker (Namespace), gVisor (User-space syscalls), and Firecracker/Kata (Hardware MicroVMs).
- **The Evaluation Shift:** Standard software testing (deterministic, binary pass/fail) fails for agents. Agent evaluation requires stochastic testing (probabilistic outcomes across multiple trials) and trajectory-based log analysis.
- **Cognitive-Executive Separation:** The most secure frameworks separate the LLM reasoning process from the action execution process entirely, placing an immutable, deterministic validator between them.

---

### ✅ Quick Revision Checklist

- [ ] I can explain why standard Docker containers fail to securely isolate agents.
- [ ] I can define gVisor and Firecracker/MicroVMs.
- [ ] I can list the 4 layers of input/output guardrails (Input, Pre-Execution, Post-Execution, Output).
- [ ] I can name at least two tracing/observability tools used for agent evaluation (e.g., LangSmith, Braintrust).
- [ ] I understand the difference between containing a blast radius (Sandboxing) and semantic policy enforcement (Pre-Action Authorization).