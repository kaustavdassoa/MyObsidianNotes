---
title: Designing Multi-Agent Systems
author: Victor Dibia
tags:
  - multi-agent-systems
  - AI
  - book-summary
  - software-engineering
  - system-architecture
---
# Designing Multi-Agent Systems

## Chapter 1: Understanding Multi-Agent Systems

> [!summary] Core Concept As tasks become increasingly complex, dynamic, and multi-domain, single [[Generative AI]] models and individual agents become insufficient, necessitating the use of [[Multi-Agent Systems]]. A multi-agent system addresses these highly complex challenges by orchestrating a group of specialized agents—each equipped with a model, memory, and tools—that collaborate to reason, act, communicate, and adapt.

### Key Architectural Patterns

- **[[Agent Anatomy Pattern]]**: The foundational architecture of an individual agent consists of three core components: an AI model serving as the reasoning engine, tools that enable concrete actions, and memory (short-term and long-term) for learning and adaptation.
    
- **Agent Architecture Decision Framework**: A spectrum of design choices ranging from simple models (for text generation), to single agents (for defined action-taking), to [[Multi-Agent Workflows]] (for structured deterministic collaboration), and finally to autonomous multi-agent systems (for exploratory problem-solving).
    
- **[[Multi-Agent Workflows]] (Defined Orchestration)**: A pattern where agent collaboration follows pre-defined, explicitly programmed execution paths, sequences, and handoffs, resulting in predictable processes.
    
- **[[Autonomous Orchestration]] (AI-Driven Orchestration)**: A pattern where control flow and orchestration decisions are determined dynamically at runtime by AI models, allowing agents to negotiate responsibilities.
    
- **[[Round-Robin Orchestration]]**: A specific autonomous pattern where agents address tasks by taking sequential turns in a shared conversation until a defined termination condition is met.
    

### Agent Communication & Workflows

- Agents fundamentally operate through an **action-perception loop**, where they iteratively take action, perceive the results, update their memory, and adapt their approach until the task is complete.
    
- Communication is handled through **conversational programming**, meaning that the agent's reasoning, tool use, and resulting actions are seamlessly represented as messages within a chat history.
    
- In multi-agent workflows, communication is passed deterministically through established handoff points, whereas in autonomous systems, communication emerges organically as agents share data and adapt based on intermediate task results.
    

### Important Code & Implementation Concepts

- **`picoagents`**: A custom Python library introduced to build individual agents, manage memory, and handle multi-agent orchestration from scratch.
    
- **`OpenAIChatCompletionClient`**: A model client class used to standardize interactions with [[LLMs]] APIs, allowing the agent to remain agnostic to the specific model provider.
    
- **`RoundRobinOrchestrator`**: A code class used to facilitate turn-taking communication between specialized agents.
    
- **Termination Conditions**: Technical implementations used to gracefully stop an orchestration loop and prevent runaway agent execution.
    
- **vLLM**: An external tool highlighted for self-hosting open-source models locally while maintaining compatibility with the OpenAI API specification.
    

> [!info] Key Takeaways
> 
> - **Task complexity dictates architecture:** Utilize multi-agent approaches specifically when tasks require multi-step planning, diverse domain expertise, extensive context processing, and adaptive solutions.
>     
> - **Agents > Models:** While AI models merely generate text, agents actively take actions, use external tools, maintain persistent context, and modify behavior based on real-world results.
>     
> - **Economic time arbitrage:** [[Multi-Agent Systems]] offer significant economic advantages by substituting cheap AI inference for expensive human labor hours on long-running tasks.
>     

## Chapter 2: Multi-Agent Patterns

> [!summary] Core Concept Multi-agent orchestration patterns exist on a spectrum ranging from developer-defined, deterministic explicit workflows to highly flexible, AI-driven autonomous coordination. Designing an effective system requires matching the right architectural pattern to your task by carefully balancing predictability, autonomy, and implementation complexity.

### Key Architectural Patterns

- **Workflow Patterns (Explicit Control)**: Systems modeled as [[Computational Graphs]] where nodes represent agents or functions, and edges define the control flow.
    
    - **Sequential Workflows**: Linear, predictable execution paths (A -> B -> C).
        
    - **Conditional Workflows**: Logic-based edges that allow dynamic branching and execution loops.
        
    - **[[Supervisor Pattern]]**: A conditional workflow variant where a central node evaluates requests and routes them to specialized agents.
        
    - **Parallel Workflows**: Concurrent execution utilizing fan-out and fan-in phases.
        
- **Autonomous Patterns (Emergent Control)**: Flow of control is determined dynamically by AI models at runtime.
    
    - **[[Plan-Based Orchestration]]**: A single "project manager" orchestrator creates plans, assigns work, and centrally monitors progress.
        
    - **[[Handoff Pattern]]**: Agents operate with local knowledge and make peer-to-peer decisions to transfer control and context.
        
    - **Conversation-Driven (Group Chat)**: Orchestration emerges naturally through dialogue in a shared conversation.
        
    - **Hierarchical Agents**: A design where a single agent is actually a composite of an entire multi-agent workflow hidden internally.
        

### Agent Communication & Workflows

- **Explicit Workflows:** Communication is highly deterministic; it passes strictly along pre-defined edges.
    
- **Plan-Based Orchestration:** Communication is centralized and curated to prevent context overload.
    
- **Handoff Patterns:** Communication is peer-to-peer; control and context are passed directly between colleagues.
    
- **Conversation-Driven Patterns:** Communication is a broadcast; all agents observe a shared conversation history.
    

### Important Code & Implementation Concepts

- **`picoagents.workflow` vs `picoagents.orchestration`**: Separates explicit computational graphs from emergent, agent-reasoned control.
    
- **`workflow.chain()`**: A method used to programmatically link steps in sequential workflows.
    
- **Orchestrator Classes**: `PlanBasedOrchestrator`, `RoundRobinOrchestrator`, and `AIOrchestrator`.
    
- **Explicit Task Management**: Implementations like `MaxMessageTermination` to prevent infinite loops and runaway token costs.
    

> [!info] Key Takeaways
> 
> - **No single "best" pattern:** Well-defined pipelines need predictable workflows; exploratory tasks require autonomous patterns.
>     
> - **Autonomy vs. Control:** Workflows provide high reliability but lack flexibility; autonomous patterns offer adaptability but can result in less deterministic behavior.
>     
> - **Embrace hybrid architectures:** Compose multiple patterns, such as using an autonomous orchestrator for high-level planning and deterministic workflows for execution.
>     

## Chapter 3: UX Principles for Multi-Agent Systems

> [!summary] Core Concept Multi-agent applications necessitate a fundamental shift from traditional "interface design" (direct manipulation) to "delegation design" (open-ended instructions to autonomous AI). UX must focus on building trust through capability discovery, cost-aware delegation, [[Observability]], and interruptibility due to the "jagged frontier" of AI reliability.

### Key Architectural Patterns

- **Delegation Design**: A paradigm where users delegate open-ended tasks to autonomous systems that decide their own action sequences from vast capability spaces.
    
- **Interactive vs. Offline Scenarios**: Distinguishing between real-time tasks requiring active human engagement (low latency) and autonomous back-office tasks that run in the background.
    
- **Three-Tier Approval System**: A cost-aware delegation pattern categorizing tasks: low-cost (automatic), medium-cost (execute and notify), and high-cost (require explicit user permission).
    

### Agent Communication & Workflows

- **Observable Communication:** Users need to see real-time coordination, such as handoffs between specialized agents, and how agents resolve internal disagreements.
    
- **[[Human-in-the-Loop]] Interruption:** Workflows must allow users to pause conversations, correct assumptions, and seamlessly update shared context without starting from scratch.
    

### Important Code & Implementation Concepts

- **PicoAgents WebUI**: A practical implementation reference for multi-agent UX principles.
    
- **Preset Example Prompts**: Facilitate "capability discovery" by highlighting reliable sample tasks.
    
- **Explicit Approval Configuration**: Interfaces that trigger approval dialogs based on developer-defined risk levels.
    
- **State Persistence**: Mechanisms allowing users to stop agent execution mid-stream and resume later.
    

> [!info] Key Takeaways
> 
> - **Design for Distinct Personas:** End users need functional transparency; developers need architectural transparency.
>     
> - **Action Consequences:** Interfaces must communicate the potential impact (cost) of actions and provide granular approval controls.
>     
> - **Observability Builds Trust:** Real-time activity streaming, simple progress narratives, and outcome attribution are essential.
>     

## Chapter 4: Building Your First Agent

> [!summary] Core Concept Building an effective framework requires starting from first principles with a minimal interface (`Agent(model, tools, memory)`). Implementing foundational design principles like async-first architecture, [[Structured Outputs]], context engineering, and middleware transforms unpredictable text generators into reliable, autonomous action-takers.

### Key Architectural Patterns

- **Async-First Architecture**: Utilizing asynchronous execution to prevent agents from idling during I/O tasks.
    
- **Event-Based Streaming**: Yielding execution progress in real-time as an event stream for debugging and UI responsiveness.
    
- **Agent Middleware Pattern**: Intercepting operations to inject logic like security filtering, logging, and [[OpenTelemetry]] tracing.
    
- **Agents as Tools Pattern**: Wrapping specialist agents within a tool interface (`as_tool()`) so they can be invoked by coordinator agents.
    
- **Memory Architectures**: Application-Managed Storage (auto-injected) vs. Agent-Managed Memory (tools to curate knowledge).
    

### Agent Communication & Workflows

- **The Agent Execution Loop**: Prepare context -> call model -> process response/execute tools -> append results -> iterate.
    
- **Hierarchical Coordinator-Specialist Workflow**: Coordinators manage overarching goals and delegate sub-tasks to specialists, which return condensed summaries to prevent context window explosion.
    
- **Dynamic [[Human-in-the-Loop]]**: Agents pause execution to request tool approval for sensitive actions, seamlessly resuming once granted.
    

### Important Code & Implementation Concepts

- **[[Pydantic]] (Structured Output)**: Forcing [[LLMs]] to return machine-readable JSON objects for reliable tool calling.
    
- **`FunctionTool` and `BaseTool`**: Abstractions converting Python functions into LLM-compatible JSON schemas.
    
- **Context Engineering Tools**: `HeadTailCompaction`, `PlanningHook`, and `LLMCompletionCheckHook` to prevent context bloat and premature stopping.
    
- **`AgentContext` and `CancellationToken`**: Managing transient session state and gracefully aborting tasks.
    
- **`OTelMiddleware`**: Auto-instrumenting agents with [[OpenTelemetry]] for distributed tracing.
    

> [!info] Key Takeaways
> 
> - **Structured outputs are key:** Strict schemas transform LLMs into precise tools.
>     
> - **Context engineering is mandatory:** Strategies like compaction and hooks prevent agents from "thrashing" or hallucinating on multi-step tasks.
>     
> - **Agent composition solves context bloat:** Independent specialists returning summaries keeps the orchestrator's context window clean.
>     

## Chapter 5: Building Computer Use Agents

> [!summary] Core Concept When programmatic APIs are insufficient, [[Computer Use Agents]] interact directly with user interfaces exactly as humans do. They utilize an [[Observe-Think-Act]] execution cycle driven by interface representation, action sequence generation, and an action executor.

### Key Architectural Patterns

- **[[Observe-Think-Act]] Execution Pattern**: The agent perceives UI state, plans an action sequence, executes the action, and repeats.
    
- **Anatomy of a Computer Use Agent**: Consists of an action sequence planner (LLM), interface representation (converting UI to text/images), and an action executor (UI tooling).
    
- **"Agent + Tools" Architecture**: Extending a base Agent class by treating interface actions (clicking, typing) as specialized tools.
    
- **Interface Representation Strategies**: Text-based (DOM parsing), Image-based (screenshots), and Hybrid approaches.
    
- **Implicit vs. Explicit Planning**: Iterative, step-by-step generation versus generating complete upfront sequences.
    

### Agent Communication & Workflows

- Operates autonomously through continuous observation and execution.
    
- Requires enhanced observation steps to re-evaluate environment state (e.g., checking for new pages or errors) after every action.
    
- The LLM breaks down high-level tasks and selects from specific UI tools to execute steps.
    

### Important Code & Implementation Concepts

- **[[Playwright]]**: The robust "action executor" used to automate web browsers.
    
- **`ComputerUseAgent` Class**: Manages browser interfaces internally with graceful error handling.
    
- **`observe_page` Tool**: Captures both textual structure (DOM) and visual appearance (screenshots).
    
- **Action Space Tool Suite**: `NavigateTool`, `ClickTool`, `TypeTool`, and `ScrollTool`.
    
- **Resource Management**: Explicitly terminating memory-heavy browser processes and running in headless mode.
    

> [!info] Key Takeaways
> 
> - **Security risks are uniquely dangerous:** Highly susceptible to HTML prompt injection; requires strict sandboxing and least-privilege credentials.
>     
> - **Representation trade-offs:** DOM parsing is cheap/precise; screenshots capture layout but are expensive; hybrid is optimal but complex.
>     
> - **Strict resource management:** Browser automation requires timeouts and cost-monitoring to prevent runaway consumption.
>     

## Chapter 6: Building Multi-Agent Workflows

> [!summary] Core Concept Multi-agent workflows are best implemented as deterministic [[Computational Graphs]] that provide developers explicit control over execution paths. Utilizing type-safe steps, conditional edges, and an intelligent runner enables highly predictable, observable, and fault-tolerant systems tailored for structured tasks.

### Key Architectural Patterns

- **[[Computational Graphs]]**: Structuring orchestration as a deterministic graph of explicit nodes (steps) and edges (transitions).
    
- **Type-Safe Step Architecture**: Utilizing strict data validation models to catch structural errors before execution.
    
- **Workflow Checkpointing**: Automatically saving execution progress at intervals, enabling resumption from failure points without re-executing expensive upstream operations.
    
- **Stateless Horizontal Scaling**: Persisting all state in checkpoints, allowing disposable worker nodes to pick up execution from a shared queue.
    

### Agent Communication & Workflows

- Interactions occur through explicit, developer-defined paths (sequential, conditional, parallel) rather than dynamic negotiation.
    
- Coordination relies on a shared `Context` object that maintains state.
    
- Execution engines handle complex dependency resolution like "fan-in" (waiting for ALL inputs) or "conditional" (waiting for ANY valid path).
    

### Important Code & Implementation Concepts

- **`Workflow`, `WorkflowRunner`, `FunctionStep`, and `Context`**: Core engine classes.
    
- **[[Pydantic]] Data Contracts**: Enforcing type-safe inputs/outputs (`NumberInput`, `NumberOutput`).
    
- **JSON Serialization**: Methods like `dump_component()` retain type info for sharing and UI integration.
    
- **Production Storage Backends**: PostgreSQL, Redis, or S3 for robust checkpoint persistence.
    

> [!info] Key Takeaways
> 
> - **Prioritize predictability:** Choose workflows when a problem is well-understood and strict audit trails are necessary.
>     
> - **Type safety prevents cascading failures:** Catch structural errors early.
>     
> - **Checkpointing is key to scale:** Guarantees fault tolerance and prevents wasted API token costs.
>     

## Chapter 7: Building Autonomous Multi-Agent Orchestration

> [!summary] Core Concept Implementing autonomous patterns requires a unified orchestrator loop and flexible termination conditions. This enables sophisticated, adaptable coordination—like round-robin, AI-driven, and plan-based orchestration—with minimal code and robust [[Observability]].

### Key Architectural Patterns

- **The Orchestrator Loop**: Repeatedly selecting the next agent, preparing context, executing, updating state, and checking termination.
    
- **Composable Termination Conditions**: Preventing runaway execution by combining simple rules (max messages) and triggers ("TERMINATE") using logical operators.
    
- **[[Round-Robin Orchestration]]**: Sequential turn-taking for balanced participation.
    
- **[[Autonomous Orchestration]] (AI-Driven)**: An LLM dynamically determines which agent should speak next based on context.
    
- **[[Plan-Based Orchestration]]**: An orchestrator generates an explicit execution plan, assigns tasks, and evaluates progress step-by-step.
    

### Agent Communication & Workflows

- Agents operate within a shared conversation history managed by the orchestrator.
    
- AI-driven workflows are highly adaptive (e.g., routing back to a researcher if more data is needed).
    
- Plan-based workflows rely on structured programmatic evaluations before advancing steps.
    

### Important Code & Implementation Concepts

- **`BaseOrchestrator`**: Abstract base class handling streaming, cancellation, and state management.
    
- **Delta-based termination checking**: Optimization tracking position to pass only new messages to the termination checker.
    
- **[[Pydantic]] (Structured Output)**: `ExecutionPlan` and `StepProgressEvaluation` transform unpredictable text into reliable data structures for programmatic retry logic.
    

> [!info] Key Takeaways
> 
> - **A unified loop powers everything:** Easy composability and error handling across complex patterns.
>     
> - **Flexible termination is vital:** Prevents infinite loops and runaway API costs.
>     
> - **Structured output equals autonomy:** Automated task decomposition requires strictly formatted data structures.
>     

## Chapter 8: Building Modern Web Experiences for Agent Applications

> [!summary] Core Concept Traditional web architectures fail multi-agent systems because these systems require real-time streaming, coordination visibility, and mid-task human interruptions. Developers must build decoupled applications utilizing an asynchronous frontend to dynamically consume and render event streams.

### Key Architectural Patterns

- **The Two-Component Architecture**: Decoupling the Backend (agent logic/streaming API) from the Frontend (UI rendering).
    
- **Python-Native UI Frameworks**: Prototyping tools like Streamlit and Chainlit.
    
- **Stateless Horizontal Scaling**: Using serializable context objects fetched/saved per interaction instead of keeping persistent state in server memory.
    
- **[[React]] Component Architecture**: Managing complex UI states (syntax highlighting, tool execution) using modern frontend patterns.
    

### Agent Communication & Workflows

- Exposed via **real-time event streaming**; agents emit continuous events (token generation, tool calls) for user observation.
    
- **Mid-task workflows**: Agents requesting clarification pause the stream, save context, and wait for human input before seamlessly resuming.
    

### Important Code & Implementation Concepts

- **[[FastAPI]] & Uvicorn**: Async-native backend handling streaming effectively.
    
- **[[Server-Sent Events (SSE)]]**: Unidirectional HTTP streaming (`text/event-stream`) recommended over WebSockets.
    
- **`AbortController` & `CancellationToken`**: Implementing interruptibility by gracefully stopping backend LLM calls when a user aborts.
    
- **React, Vite, Tailwind**: Recommended modern frontend stack.
    

> [!info] Key Takeaways
> 
> - **SSE > WebSockets:** SSE combined with stateless context provides real-time responsiveness without massive infrastructure complexity.
>     
> - **Translate UX into code:** UI must actively implement capability discovery, observability, and interruptibility.
>     
> - **Keep agent logic ignorant of the UI:** Clean API layers allow the same agent to be used via CLI, Web UI, or Slack.
>     

## Chapter 9: But What About Multi-Agent Frameworks?

> [!summary] Core Concept While building from scratch provides deep understanding, adopting a production framework is more practical for enterprise scale due to standardized components and operational infrastructure. Evaluate frameworks systematically against ten core capabilities rather than fleeting feature lists.

### Key Architectural Patterns

- **The Ten Core Framework Capabilities**: Intuitive API design, async-first execution, state management, declarative configuration, robust middleware pipelines, etc.
    
- **Production vs. Educational**: Production architectures (Microsoft Agent Framework, Pydantic AI) provide auto-scaling, cloud integration, and plugin-based guardrails.
    
- **Hybrid Approaches**: Combining rapid framework prototyping with custom development for core business logic.
    

### Agent Communication & Workflows

- Frameworks must flexibly support both explicit control workflows and autonomous emergent patterns.
    
- Interactions must be backed by comprehensive state management and robust guardrail infrastructures to monitor transitions securely.
    

### Important Code & Implementation Concepts

- **Async and Streaming Interfaces**: Non-blocking execution natively supported by frameworks.
    
- **Event Streams for [[Observability]]**: `ModelCallEvent`, `ToolCallEvent`.
    
- **Component Serialization**: `to_config()` / `from_config()` for version control and low-code tooling.
    

> [!info] Key Takeaways
> 
> - **Evaluate capabilities, not tools:** Focus on observability, testing, and state management.
>     
> - **Reduce maintenance burden:** Frameworks handle API changes, security patches, and onboarding.
>     
> - **Mind the infrastructure gap:** Enterprise frameworks are required for auto-scaling and safety guardrails.
>     

## Chapter 10: Evaluating Multi-Agent Systems

> [!summary] Core Concept Multi-agent systems must be evaluated on complete "trajectories"—the full sequence of reasoning, tool calls, and coordination—rather than just the final outcome. Adopt [[Evaluation-Driven Development (EDD)]] to define success criteria before writing code.

### Key Architectural Patterns

- **[[Evaluation-Driven Development (EDD)]]**: A 5-step methodology: Define Success Criteria -> Create Task Suite -> Choose Metrics -> Establish Baselines -> Plan Iteration Workflow.
    
- **Observability-First Architecture**: Systems emit structured events natively so every run generates an evaluable trajectory.
    
- **[[LLM-as-a-Judge]]**: Using strong models to assess nuanced, reference-free qualities (helpfulness, formatting) traditional metrics miss.
    
- **Scoring Strategies**: Single Answer Grading, Pairwise Comparison, Dimensional Scoring.
    

### Agent Communication & Workflows

- Interactions are captured entirely as trajectories.
    
- Evaluation proves multi-agent communication introduces massive token overhead; complex workflows are only justified for tool-heavy research, while simple tasks perform better with direct model calls.
    

### Important Code & Implementation Concepts

- **`RunTrajectory`**: Data structure capturing tasks, messages, and metadata.
    
- **Answer Extraction Strategies**: Programmatically extracting the actual result using configs like `answer_strategy="last_assistant"`.
    
- **`LLMEvalJudge` and `ExactMatchJudge`**: Evaluator classes for reference-free and reference-based metrics.
    
- **Verbosity Penalty Correction**: Adjusting prompts to prevent evaluator models from penalizing transparent multi-agent coordination as "verbose".
    

> [!info] Key Takeaways
> 
> - **Evaluate trajectories:** Traditional metrics fail on heterogeneous artifacts; measure the whole process.
>     
> - **Build evaluation harness first:** Defines what "good" looks like to prevent misaligned development.
>     
> - **Beware LLM Judge biases:** Ensure evaluators don't unfairly penalize valid reasoning chains.
>     
> - **Don't over-engineer:** Multi-agent systems add cost; use only when explicitly necessary.
>     

## Chapter 11: Optimizing Multi-Agent Systems

> [!summary] Core Concept Optimizing systems requires a loop of measuring, analyzing, modifying, and validating. Developers should prioritize tuning high-leverage "Agent-system parameters" (instructions, tools, patterns) before attempting complex "Model-level optimizations" (fine-tuning).

### Key Architectural Patterns

- **The Optimization Loop**: Measure -> Analyze -> Modify -> Validate -> Repeat.
    
- **Two-Level Optimization Framework**: Distinguishes between application-level (Agent-system) and foundational (Model-level) parameters.
    
- **Model Optimization Decision Framework**: Start with tuning system parameters using frontier models; progress to fine-tuning only if specific domain failures persist.
    
- **Pattern Selection Guide**: Mapping task characteristics to the right orchestration pattern to prevent unnecessary overhead.
    

### Agent Communication & Workflows

- Select the simplest effective communication pattern to minimize token costs.
    
- Explicit tool-based communication for task completion (e.g., returning 'complete' status) prevents runaway loops.
    
- **Metacognition (reflection loops)**: Agents systematically review progress and adapt plans before acting.
    

### Important Code & Implementation Concepts

- **`TaskStatusTool`**: Lets agents explicitly signal task completion or failure.
    
- **`EvalRunner` and `ExactMatchJudge`**: The minimum viable evaluation harness required for tracking metrics.
    
- Contrasting `SequentialWorkflow` vs. `AIOrchestrator` to demonstrate the anti-pattern of using expensive AI orchestration for linear tasks.
    

> [!info] Key Takeaways
> 
> - **Optimize system before model:** Instructions, tools, and patterns provide higher ROI than fine-tuning.
>     
> - **Curate high-quality tools:** Too many mediocre tools confuse the agent and waste context window.
>     
> - **Implement metacognition:** Reflection loops let agents abandon compromised trajectories.
>     
> - **Measure to optimize:** Evaluation frameworks are absolute prerequisites for optimization.
>     

## Chapter 12: Protocols for Distributed Agents

> [!summary] Core Concept As systems scale, they require distributed architectures allowing agents to communicate across boundaries. Standardized protocols like the [[Model Context Protocol (MCP)]] and [[Agent-to-Agent Protocol (A2A)]] manage discovery, state, and security across these disparate networks.

### Key Architectural Patterns

- **Distributed Agent Architecture**: Agents and tools run in separate execution contexts but communicate over a network, improving scalability and isolation.
    
- **[[Model Context Protocol (MCP)]]**: Client-server model connecting MCP Servers (tools/resources), MCP Clients, and MCP Hosts (orchestrators).
    
- **[[Agent-to-Agent Protocol (A2A)]]**: Peer-to-peer federated architecture where agents discover and invoke remote agents as opaque black boxes.
    
- **Inter-Agent Context Management Patterns**: Orchestrator as Central Hub vs. Shared State via External Storage.
    

### Agent Communication & Workflows

- **MCP Workflows:** Orchestrator-specialist model where host applications route requests to tools with high interactivity and real-time status.
    
- **A2A Workflows:** Peer-to-peer autonomous interaction relying on strict task lifecycle states and `contextId` maintenance.
    

### Important Code & Implementation Concepts

- **FastMCP API**: Using `@mcp.tool()` decorators for registration.
    
- **MCP Tool Bridge**: Adapting remote tools into standard `BaseTool` interfaces.
    
- **Agent Cards (`agent-card.json`)**: A2A JSON documents declaring identity, endpoints, and capabilities.
    
- **Security Protocols**: OAuth 2.1 with PKCE for MCP; standard web OAuth2 and skill-based scopes for A2A.
    

> [!info] Key Takeaways
> 
> - **Distributed logic at the application layer:** Agents make autonomous routing and coordination decisions natively.
>     
> - **Unique protocol requirements:** Need built-in support for streaming, resumability, and human-in-the-loop interactions.
>     
> - **MCP for Internal, A2A for External:** Use MCP for composed internal orchestration; use A2A for cross-organizational black-box delegation.
>     

## Chapter 13: Ethics and Responsible AI for Multi-Agent Systems

> [!summary] Core Concept When AI shifts to autonomous action-taking, ethical challenges move from data bias to behavioral uncertainty and emergent risks. Deployment requires defense-in-depth security, strict [[Human-in-the-Loop]] policies, and robust accountability management.

### Key Architectural Patterns

- **The Agents Rule of Two**: An agent should satisfy no more than two of: (A) Process untrustworthy inputs, (B) Access sensitive systems, (C) Change state/communicate externally. All three mandate human oversight.
    
- **Defense Through Middleware**: Layered defense intercepting at input inspection, tool authorization, and output validation.
    
- **Human Oversight Thresholds**: Mandating explicit approval for irreversible actions (e.g., financial transactions).
    

### Agent Communication & Workflows

- Interactions exhibit emergent, unpredictable behaviors that were never explicitly programmed.
    
- **"Many Hands Problem"**: Accountability diffuses across autonomous actors, making harm an emergent property of collective interaction.
    
- Need for architectural constraints or "circuit breakers" to halt interactions exceeding safety bounds.
    

### Important Code & Implementation Concepts

- **`SecurityMiddleware`**: Blocking prompt injection via regex payload scanning.
    
- **`auto_approve_policy`**: Conditional authorization auto-approving safe operations while flagging high-risk ones.
    
- **`OutputValidationMiddleware`**: Redacting sensitive data and blocking exfiltration payloads.
    
- **Least Privilege Isolation**: Using HashiCorp Vault or AWS Secrets Manager instead of environment variables.
    

> [!info] Key Takeaways
> 
> - **Risk scales with action:** Jailbreaks evolve from offensive text to catastrophic system breaches.
>     
> - **Beware Agentic Noise:** Agents can overwhelm digital platforms (e.g., flooding review systems), breaking system equilibrium.
>     
> - **Multi-tenant security breakdown:** Agents actively explore environments and may exploit shared infrastructure credentials.
>     
> - **Product signals != Quality:** Optimizing purely for engagement trains "sycophantic" agents that autonomously execute harmful, user-affirming decisions.
>     

## Chapter 14: Answering Business Questions from Unstructured Data

> [!summary] Core Concept Answer ambiguous business questions by transforming unstructured data tasks into structured, deterministic workflows rather than relying on emergent agent behaviors. Combining cost-effective pre-filtering with structured LLM analysis extracts reliable semantic insights at scale.

### Key Architectural Patterns

- **Sequential Workflow Pattern**: A deterministic 4-stage pipeline: Load/Cache Data -> Keyword Filtering -> LLM Analysis -> Insight Generation.
    
- **Two-Stage Filtering Pattern**: Applying cheap operations (regex) to drastically reduce dataset size before routing to expensive LLM analysis.
    
- **Type-Safe Pipeline**: Using strict [[Pydantic]] data models to enforce consistency between workflow steps.
    

### Agent Communication & Workflows

- Workflow relies entirely on linear, sequential interaction. No dynamic peer-to-peer negotiation.
    
- Information flows deterministically; execution is predefined, allowing independent testing.
    

### Important Code & Implementation Concepts

- **`Workflow().chain()`**: Programmatically linking sequential stages.
    
- **Regex Pre-filtering**: Using word boundaries (`\b`) and case insensitivity to catch keywords cheaply.
    
- **[[Pydantic]] Structured Outputs**: Enforcing schemas via the `output_format` parameter to eliminate parsing errors.
    
- **Incremental Checkpointing**: Resuming interrupted batches without repurchasing API calls.
    
- **Chain-of-Thought Rationale Fields**: Prompting for explicit explanation fields (`ai_rationale`) instead of probabilistic confidence scores to reduce hallucination.
    

> [!info] Key Takeaways
> 
> - **Pre-filter to control costs:** Always apply cheap heuristics before LLM calls.
>     
> - **Never parse free-form text:** Pydantic schemas eliminate parsing errors entirely.
>     
> - **Checkpoint everything:** Ensures resumability across inevitable production failures.
>     
> - **Test components independently:** Structured workflows isolate filtering, analysis, and aggregation for high reliability validation.
>     

## Chapter 15: Building a Software Engineering Agent

> [!summary] Core Concept Software engineering requires exploration and adaptive problem-solving that cannot fit fixed workflows. Addressing this requires an autonomous agent equipped with powerful tools, clear instructions, and memory to dynamically reason, plan, and verify execution.

### Key Architectural Patterns

- **Autonomous Pattern**: Control flow is driven by dynamic model reasoning.
    
- **Agent + Tools + Memory Pattern**: The foundational architecture behind coding assistants like GitHub Copilot, Cursor, and Windsurf.
    
- **Explicit Workflow Structure via Prompts**: Structuring the prompt into numbered phases (memory check, planning, execution, verification, completion) to guide autonomy instead of hardcoding edges.
    

### Agent Communication & Workflows

- Immediate feedback loop: **Write -> Test -> Fix**, relying on execution tools to verify code.
    
- Workflow progresses strictly guided by prompt instructions: checking memory -> generating machine-readable todo lists -> executing -> verifying tests -> checking completion criteria.
    

### Important Code & Implementation Concepts

- **File Operation Tools**: `read_file`, `write_file`, `list_directory`, `grep_search`.
    
- **Execution Tools**: `python_repl` for snippets, `bash_execute` for test suites.
    
- **`MemoryTool`**: Persistence across conversations (`view`, `str_replace`).
    
- **Meta-Cognitive Tools**: `ThinkTool`, `TodoWriteTool`, `TodoReadTool` for explicit tracking.
    
- **Context Engineering Hooks**: Managing token budgets and preventing premature stopping.
    
- **Workspace Isolation**: Scoping file operations to prevent system file modification outside the project.
    

> [!info] Key Takeaways
> 
> - **Prompts provide guidance:** Tools give capability, but strict completion criteria in prompt engineering drive success.
>     
> - **Execution tools prevent blindness:** Agents need `bash_execute` to verify tests and debug effectively.
>     
> - **Context engineering scales tasks:** Compaction and isolation prevent hallucinations on deep tasks like multi-file code reviews.
>     
> - **Metacognition prevents randomness:** Todo list tools give agents a machine-readable record, ensuring systematic execution.
>