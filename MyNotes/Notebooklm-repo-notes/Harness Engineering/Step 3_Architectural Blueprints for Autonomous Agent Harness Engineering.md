**Systematic Literature Review: Agentic Harness Engineering Methodologies**

The transition of Large Language Models (LLMs) from passive text generators to autonomous agents has shifted the engineering bottleneck from the model's internal intelligence to its execution environment, formally known as the "agent harness". A harness provides the infrastructure that manages an agent's context, state, tool execution, and security boundaries.

Below is a detailed matrix categorizing the current methodologies in harness engineering, followed by an analysis of the academic consensus and conflicting paradigms.

### **Methodology Categorization Matrix**

|**Approach**|**Description & Core Mechanics**|**Representative Frameworks & Techniques**|
|:--|:--|:--|
|**Cognitive-Executive Separation (Architectural Containment)**|Structurally isolates the agent's LLM reasoning process from the system's execution process at the OS level. The agent operates in a fully isolated namespace and can only "propose" actions, possessing zero execution surface of its own.|**Parallax:** Uses a 4-tier "graduated determinism" validation shield between the agent and host. **OpenShell:** Enforces kernel-level constraints via BPF and seccomp.|
|**Sandboxing & Environmental Isolation**|Contains the blast radius of agent actions by restricting filesystem, kernel, and network access. Moves beyond standard containers to hardware-backed or user-space kernel virtualization.|**Firecracker / Kata Containers:** MicroVMs with dedicated guest kernels. **gVisor:** User-space syscall interception. **WebAssembly (WASM):** Capability-based lightweight sandboxes.|
|**Deterministic Pre-Action Guardrails**|Intercepts proposed tool calls synchronously before execution and evaluates them against declarative, non-LLM policies (e.g., spending limits, data access bounds).|**Open Agent Passport (OAP):** Verifies cryptographic identity and capability scope prior to execution. **PSG-Agent:** Implements multi-point defense (Input Guard, Plan Monitor, Tool Firewall).|
|**Identity Governance & Authorization Propagation**|Manages how permissions flow across multi-agent systems and multi-step workflows. Ensures that when one agent delegates to another, the authority transfer is attenuated, auditable, and revocable.|**IBCTs (Invocation-Bound Capability Tokens):** Append-only token chains that fuse identity and attenuated authorization. **PCAS:** Enforces dependency-graph policies over tool calls.|
|**Formal Verification & Model Checking**|Treats the LLM as a non-deterministic oracle within a finite-state machine (FSM) orchestration layer. Uses rigorous mathematical logic to verify multi-step safety properties rather than relying on probabilistic evaluations.|**AgentVerify:** Uses Linear Temporal Logic (LTL) to check properties like memory integrity, tool constraints, and human-in-the-loop boundaries through hybrid runtime and post-hoc analysis.|
|**Adaptive & Self-Evolving Harnesses**|Automates the design and improvement of the harness itself. An "evolver" agent analyzes execution telemetry (e.g., failures, costs, latency) to dynamically rewrite tool schemas, prompts, and routing rules without altering model weights.|**Meta-Harness:** End-to-end search-based optimization of harness code. **Agentic Harness Engineering (AHE):** Observability-driven automatic evolution.|

---

### **Overlapping Consensus Among Authors**

**1. Model Alignment is Insufficient for Autonomous Execution** Researchers universally agree that alignment training (e.g., RLHF, Constitutional AI) is probabilistic and inherently flawed as a primary security measure for agents. Because agents interact with dynamic external environments, prompt injections or logic errors can bypass alignment. Security must be enforced structurally at the harness layer, independent of the model's cooperativeness.

**2. Standard Docker Containers are a False Security Boundary** There is a strong consensus that basic Docker/runc containers are fundamentally inadequate for AI isolation. Because containers share the host's Linux kernel, a capable frontier model or an adversarial payload can orchestrate sandbox escapes via kernel flaws or misconfigurations. Authors agree that production deployments require stronger isolation, such as microVMs (Firecracker/Kata) or userspace kernels (gVisor).

**3. The Necessity of the Plan-Execute-Verify (PEV) Loop** Across frameworks, there is agreement that autonomous tasks cannot be completed linearly. The harness must impose a control loop where the agent plans, executes actions in a sandbox, and relies on deterministic sensors (compilers, linters, HTTP responses) to verify outcomes and trigger self-correction or human escalation before proceeding.

---

### **Conflicting Methodologies and Blind Spots**

**1. Application-Layer Interception vs. Architectural Separation** A major architectural conflict exists regarding _where_ to enforce security.

- **Application-Layer Advocates:** Frameworks like LangGraph, AgentSpec, and AEGIS rely on middleware hooks and programmatic callbacks within the agent's application code to filter actions.
- **Architectural Separation Advocates:** Authors of the Parallax paradigm argue that application-level interception is fatally flawed. Because the interception mechanism shares the same OS process and trust domain as the agent, a compromised LLM can theoretically blind or modify the logging and auditing systems. They demand strict OS-level privilege separation (Cognitive-Executive Separation) where the reasoning process and the execution/validation process run in entirely different namespaces.

**2. Probabilistic Safety vs. Deterministic Containment** There is tension in how to evaluate whether a proposed action is safe.

- **LLM-as-a-Judge (Probabilistic):** Many systems (e.g., Safiron, AgentGuardian) use smaller, fine-tuned "guardian" LLMs to classify the intent of an action or monitor the plan dynamically.
- **Deterministic Gates:** Critics argue that using an LLM to guard an LLM simply shifts the attack surface to the guardian model, which can also be manipulated via adversarial payloads. They advocate for "Tier 0" deterministic guardrails—rigid, code-based policies (e.g., spending limits, allowed domains) that fail closed and require zero machine learning inference to execute.

**3. Harness Self-Evolution vs. Regression Risks** While automated harness optimization (e.g., Meta-Harness) is praised for discovering highly efficient runtime configurations that humans might overlook, there is significant conflict regarding its safety in production. Critics warn that automated harness mutation introduces severe regression risks—where updating a tool schema to fix one failure silently degrades performance on previously solved tasks or weakens security permission boundaries. They argue that harness self-evolution cannot be deployed safely without formal mathematical verification, immutable change contracts, and canary deployment pipelines.