The **"Agent Tools & Interoperability with Model Context Protocol (MCP)"** whitepaper focuses on how AI agents interact with the external world using tools, and how the Model Context Protocol standardizes these interactions. Here is a summary of the core topics:

**1. The Role and Types of Tools** Without tools, foundation models are merely pattern prediction engines isolated from real-world data. Tools act as the "hands and eyes" of an agent, allowing them to retrieve data or execute actions. Tools are categorized into:

- **Information Retrieval:** Fetching structured (e.g., databases) or unstructured (e.g., web searches) data.
- **Action / Execution:** Performing real-world operations like sending emails or running code.
- **Agent Tools:** Using another sub-agent as a tool to handle specific tasks.

**2. Tool Design Best Practices** To ensure an LLM uses tools effectively, developers must design them carefully:

- **Clear Documentation:** Use descriptive names, detail all parameters, and provide targeted examples in the tool's docstring.
- **Describe Actions, Not Implementations:** System instructions should tell the agent _what_ to achieve rather than dictating exactly _how_ to use the tool.
- **Granularity and Conciseness:** Tools should represent specific tasks (not raw API calls) and avoid returning massive datasets that bloat the LLM's context window.

**3. The Model Context Protocol (MCP)** Historically, connecting agents to tools required custom connectors, creating an unsustainable "N x M" integration problem. Anthropic introduced MCP to provide a universal, plug-and-play standard for agent-tool communication.

- **Architecture:** It uses a client-server model over JSON-RPC. The **Host** manages the agent, the **Client** maintains the connection, and the **Server** provides the tools.
- **Capabilities:** While MCP supports servers providing _Resources_ and _Prompts_, or clients providing _Sampling_ and _Elicitation_, **Tools** are the primary and most widely adopted capability.

**4. Strategic Advantages and Challenges of MCP**

- **Advantages:** MCP accelerates development, creates a reusable ecosystem of tools, allows dynamic tool discovery at runtime, and promotes modular system design.
- **Challenges:** Pre-loading metadata for thousands of tools causes **Context Window Bloat**, which increases costs and degrades reasoning quality. Furthermore, "pure" MCP lacks enterprise-grade features for authentication, identity management, and observability, meaning enterprises must build their own centralized gateways.

**5. MCP Security Risks and Mitigations** Connecting agents to external systems via MCP introduces a new threat landscape. Key risks include:

- **Dynamic Capability Injection:** Servers maliciously altering tool offerings at runtime. Mitigated by using strict allowlists and API gateways.
- **Tool Shadowing:** Malicious tools using descriptions designed to trick the agent into using them instead of legitimate tools (e.g., stealing sensitive data). Mitigated by preventing naming collisions and requiring Human-in-the-Loop approvals for sensitive actions.
- **Malicious Content:** Tools returning data that contains prompt injections. Mitigated by input/output sanitization.
- **The Confused Deputy Problem:** An agent is tricked by a malicious user prompt into misusing the MCP server's high-level privileges to perform unauthorized actions. Mitigated by applying the principle of least privilege and using scoped credentials.


___
Additional Notes 

![](../../../../Notes/ZAttachments/Pasted%20image%2020260328185809.png)

### **Tool Call**

#### **What is a tool ?**

A tool is a function or a program an LLM-based application can use to accomplish a task outside the model's capabilities.

#### **Types of tools:** 
- **Functional Tools** - allow the developer to define external functions that the model can call as needed.
- **Built-in Tools** - Some foundation models offer the ability to leverage built in tools, where the tool definition is given to the model implicitly, or behind the scenes of the model service.
- **Agent Tools** - An agent can also be invoked as a tool. This prevents a full handoff of the user conversation, allowing the primary agent to maintain control over the interaction and process the subagent's input and output as needed.

#### **Taxonomy of Agent Tools**

* **Information Retrieval**: Allow agents to fetch data from various sources, such as web
	searches, databases, or unstructured documents.
* ***Action / Execution**:  Allow agents to perform real-world operations: sending emails,
	posting messages, initiating code execution, or controlling physical devices.
* **System / API Integration**: Allow agents to connect with existing software systems and
	APIs, integrate into enterprise workflows, or interact with third-party services.
* **Human-in-the-Loop**: Facilitate collaboration with human users: ask for clarification, seek
	approval for critical actions, or hand off tasks for human judgment.

#### **Best Practices**

- User a clean name
- Describe all input and output parameters
- Simplify parameters lists : Long parameter lists can confuse the model; keep them
	parameter lists short and give parameters clear names.
- Clarify tool descriptions : 
- Add targeted examples : 
- Provide default values :
- Describe what, not how
- Don't duplicate instructions
- Don't dictate workflows :  Describe the objective, and allow scope for the model to use
   tools autonomously, rather than dictating a specific sequence of actions.
* DO explain tool interactions 
* Publish tasks, not API calls 
* Make tools as granular as possible.
* Don't return large responses - Large responses quickly swamp the output context of an LLM. Also, tool responses are stored in agent's conversation history, so large responses can impact subsequent requests as well.
* Provide descriptive error messages : overlooked opportunity for refining and documenting tool
  capabilities.

### **Understanding the Model Context Protocol (MCP)**

By standardizing this communication layer, MCP aims to decouple the AI agent from the specific implementation details of the tools it uses, allowing for a more modular, scalable, and efficient ecosystem.

#### MCP Components 
The core MCP components are the **Host**, the **Client**, and the **Server**.

* **MCP Host**: The application responsible for creating and managing individual MCP clients;
	may be a standalone application, or a sub-component of a larger system such as a multiagent
	system. Responsibilities include managing the user experience, orchestrating the
	use of tools, and enforcing security policies and content guardrails.

*  **MCP Client**: A software component embedded within the Host that maintains the
	connection with the Server. The responsibilities of the client are issuing commands,
	receiving responses, and managing the lifecycle of the communication session with its
	MCP Server.

* **MCP Server**: A program that provides a set of capabilities the server developer wants
	to make available to AI applications, often functioning as an adapter or a proxy for an
	external tool, data source, or API. Primary responsibilities are advertising available tools
	(tool discovery), receiving and executing commands, and formatting and returning
	results. In enterprise contexts, servers are also responsible for security, scalability
	and governance.

#### MCP Protocol 

**Base Protocol:** MCP uses JSON-RPC 2.0 as its base message format. This gives it a lightweight, text-based, and language-agnostic structure for all communications.

**Message Types:** The protocol defines four fundamental message types that govern the interaction flow:

* **Requests**: An RPC call sent from one party to another that expects a response.
* **Results**: A message containing the successful outcome of a corresponding request.
* **Errors**: A message indicating that a request failed, including code and description.
* **Notifications**: A one-way message that does not require a response and cannot be
  replied to.

**Transport Mechanisms:** MCP also needs a standard protocol for communication between
the client and server, called a "transport protocol", to ensure each component is able to
interpret the other's messages. 

MCP supports two transport protocols - one for **local communication** and one for **remote communication**. 

* **stdio (Standard Input/Output)**: Used for fast and direct communication in local environments where the MCP server runs as a subprocess of the Host application; used when tools need to access local resources such as the user's filesystem.

* **Streamable HTTP**: Recommended remote client-server protocol.  It supports SSE
  streaming responses, but also allows stateless servers and can be implemented in a plain
  HTTP server without requiring SSE.

![](../../../../Notes/ZAttachments/Pasted%20image%2020260327152034.png)

#### MCP Capabilities 
The Model Context Protocol (MCP) defines key primitives or capabilities to enhance how LLM-based applications interact with external systems. These capabilities are divided into those offered by the server to the client, and those offered by the client to the server.

**Server-Side Capabilities**

- **Tools**: This is the primary and most widely supported MCP capability. Tools provide a standardized way for a server to describe executable functions (such as `read_file` or `get_weather`) that the client can use, complete with name, description, and JSON schemas for inputs and outputs.
- **Resources**: These provide contextual data—such as log files, database records, database schemas, or images—that the host application can access and use.
- **Prompts**: Servers can provide reusable prompt templates or examples related to their tools and resources. These are intended to be retrieved by the client to interact directly with an LLM, though they introduce security considerations in enterprise environments.

**Client-Side Capabilities**

- **Sampling**: This reverses the typical flow of control by allowing the MCP server to request an LLM completion from the client. For example, a server could fetch a large document and use sampling to ask the client's core AI model to summarize it.
- **Elicitation**: This acts as a mechanism for the server to pause an operation and request additional information from the human user via the client's user interface. While it allows the server to dynamically gather needed input, the specification notes that servers must not use this capability to request sensitive information.
- **Roots**: This capability allows the client to define the boundaries within which a server is permitted to operate, such as restricting access to specific areas of a filesystem.

### Server Capabilities (Server ➡️ AI)

These are the primitives an MCP Server exposes to give the AI context and actionability.

| **Feature**   | **Code Decorator** | **What It Is**                                               | **Best Used For**                                   | **Example**                      |
| ------------- | ------------------ | ------------------------------------------------------------ | --------------------------------------------------- | -------------------------------- |
| **Tools**     | `@mcp.tool()`      | Executable functions that take arguments and return results. | Taking action, computing, or filtering data.        | `run_sql_query(id=102)`          |
| **Resources** | `@mcp.resource()`  | Read-only static or dynamic data exposed via a URI.          | Providing background context or reference material. | `file:///app/settings.json`      |
| **Prompts**   | `@mcp.prompt()`    | Reusable, parameterized LLM instruction templates.           | Standardizing user interactions and SOPs.           | A standard `Code_Review` prompt. |

### Client Capabilities (Client ➡️ Server)

These are advanced bidirectional features where the Server requests help from the host Client.

| **Feature**     | **What It Is**                                                                          | **Why It's Useful**                                                              |
| --------------- | --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Sampling**    | The Server asks the Client's LLM to process some data and return a completion.          | Lets the server use the AI's reasoning without needing its own separate API key. |
| **Elicitation** | The Server pauses execution and prompts the Client to ask the user for missing info.    | Gathers required parameters (via UI forms) dynamically during a workflow.        |
| **Roots**       | The Client tells the Server which local filesystem directories it is allowed to access. | Sets boundaries so the AI focuses only on the relevant workspace or project.     |

#### **MCP Advantage and Limitation** 

The Model Context Protocol (MCP) introduces significant enhancements to how AI agents interact with external systems, but its decentralized design also introduces new architectural and security challenges.

**Advantages and New Capabilities of MCP**

- **Solving the "N x M" Integration Problem**: MCP replaces the need to build custom, one-off connectors for every model and tool pairing with a single, plug-and-play universal interface.
- **Accelerating Development & Ecosystems**: It fosters a reusable, shareable ecosystem of tools, supported by public registries that standardize tool discovery.
- **Dynamic Capabilities**: Instead of hard-coding tools, MCP allows agents to dynamically discover available tools at runtime, expanding their autonomy.
- **Architectural Flexibility**: By decoupling the agent from the tool implementation, MCP promotes a modular "*agentic AI mesh.*" This makes it easier to debug, scale, or swap out underlying LLM providers without re-architecting the system.
- **Standardized Primitives**: It introduces standard server-side capabilities (Tools for execution, Resources for fetching contextual data, Prompts for templates) and client-side capabilities (Sampling for offloading LLM requests, Elicitation to gather user inputs, and Roots to set filesystem boundaries).

**Pitfalls, Shortcomings, and Challenges**

- **Context Window Bloat**: Pre-loading the definitions and schemas for thousands of tools into the LLM's context window consumes massive amounts of tokens, which increases cost and latency.
- **Degraded Reasoning**: An overloaded context window can confuse the model, causing it to ignore relevant tools, invoke incorrect ones, or lose track of the user's initial intent.
- **Enterprise Readiness Gaps**: "Pure" MCP lacks essential enterprise-grade features. It does not have robust, standardized authentication (its OAuth implementation conflicts with some modern practices), clear identity management (distinguishing between a user, an agent, or a system), or native observability (logging and tracing).
- **Stateful Connection Management**: Relying on stateful, persistent connections (like HTTP+SSE) complicates architectures that traditionally rely on stateless REST APIs, making horizontal scaling and load balancing harder.

**Security Risks**

- **Dynamic Capability Injection**: Because MCP servers can change their offered tools at runtime without notifying the client, an agent might suddenly inherit unapproved or dangerous capabilities.
- **Tool Shadowing**: Attackers can create malicious tools with deceptive descriptions (e.g., a "save_secure_note" tool) designed to trick the agent's planner into using them instead of the legitimate corporate tool, leading to the interception of sensitive data.
- **Malicious Content and Prompt Injections**: Tools might ingest external content containing prompt injections, which manipulate the agent, or return data that causes the agent to inadvertently leak proprietary information to the user.
- **Sensitive Information Leaks**: The conversation context or tools like "Elicitation" can unintentionally pass highly sensitive user data (like PII or API keys) to third-party MCP servers.
- **Coarse-Grained Authorization**: MCP only supports a one-time authorization flow. It lacks support for limiting access scope on a per-tool or per-resource basis, meaning an agent often has broad access rather than least-privilege access.
- **The Confused Deputy Problem**: Because MCP acts as a highly privileged intermediary, a malicious user prompt can trick the AI model into misusing the MCP server's authority to perform unauthorized actions, such as extracting code from a private repository.

#### **Foundations for Governance and Control**

While the Model Context Protocol (MCP) currently has limited native security features, its architecture establishes the necessary groundwork for robust enterprise governance and control. 

The foundations for this include:

- **Centralized Policy Enforcement:** Security policies and access controls can be embedded directly within the MCP server. This creates a single point of enforcement that ensures any connecting agent adheres to predefined rules, allowing organizations to tightly control what data and actions are exposed to their AI agents.
- **Philosophical Foundation for Responsible AI:** The MCP specification explicitly promotes user consent and control as a core design principle. It recommends that host applications obtain explicit user approval before invoking any tool or sharing private data.
- **Human-in-the-Loop Workflows:** By requiring user consent, the protocol encourages "human-in-the-loop" workflows. In this setup, an agent can propose an action, but it must wait for human authorization before executing it, which serves as a critical safety layer for autonomous systems.

#### **Security in MCP**

The Model Context Protocol (MCP) introduces a complex new threat landscape because it acts as both a new API surface and a broadly applied standard protocol for connecting AI agents to external systems,. Exposing systems via MCP without traditional API security controls elevates the risk of unauthorized actions and data exfiltration,. Securing MCP requires a multi-layered, proactive defense strategy to address several critical vulnerabilities:

**Dynamic Capability Injection** Because MCP servers can dynamically alter the tools and resources they offer without notifying the client, an agent might suddenly inherit unapproved and dangerous capabilities. For example, a low-risk poetry agent could unexpectedly be granted the ability to execute financial transactions.
**Mitigations:** Organizations should enforce explicit allow lists for tools, require mandatory change notifications from servers, pin tool definitions to specific versions, utilize secure API/Agent gateways to filter traffic, and host MCP servers in controlled environments,.

**Tool Shadowing** Attackers can create malicious tools with deceptive descriptions (e.g., `save_secure_note`) engineered to trick the agent's planner into utilizing them instead of the legitimate, secure corporate equivalent,,. This overshadowing can lead to the silent interception of highly sensitive user data,.
**Mitigations:** Systems must prevent naming collisions, employ Mutual TLS (mTLS) to verify identities, establish deterministic policy enforcement, restrict access to unauthorized servers, and mandate Human-in-the-Loop (HIL) approvals for any high-risk operations like data modification or network egress,.

**Malicious Tool Definitions and Consumed Contents** Tools can ingest external data containing prompt injections that manipulate the agent's behavior, even if the tool itself is benign. Additionally, tool outputs can inadvertently leak confidential company information or personal data.
**Mitigations:** Implement robust input validation and output sanitization (using services like Model Armor) to catch prompt injections, active content, and PII. Developers should also clearly separate system prompts from user inputs and require explicit user consent for consuming external resources.

**Sensitive Information Leaks** During an interaction, MCP tools may receive sensitive user data inadvertently transmitted through the conversation context or intentionally requested via the "Elicitation" capability, leading to data leaks to third-party servers.
**Mitigations:** Tool outputs carrying sensitive data should use structured formats with annotations to identify them as sensitive. Applying "Taint Sources/Sinks" helps track the flow of data, ensuring that inputs and outputs affected by untrusted sources are appropriately tagged and monitored.

**Lack of Access Scope Limits** MCP natively supports only coarse-grained, one-time client-server authorization, lacking the ability to restrict access on a per-tool or per-resource basis. This often leaves agents with far more privileges than necessary.
**Mitigations:** Tool invocations must use strictly scoped credentials and rigorously enforce the principle of least privilege. Crucially, secrets, keys, and credentials must be kept entirely out of the agent's context and transmitted via secure side channels.

**The Confused Deputy Problem** Because an MCP server acts as a highly privileged intermediary with access to critical enterprise systems, an attacker can use prompt injection to confuse the AI model. The attacker tricks the AI into misusing the MCP server's authority to perform unauthorized tasks on their behalf, such as accessing and exfiltrating proprietary code from a secure repository.
**Mitigations:** Enforce the *Principle of Least Privilege*, Agents and tools should never use a single, broadly privileged credential to access multiple systems. *Implement Scoped Credentials*, Tool invocations should rely on strictly scoped credentials that are bound to authorized callers and have short expiration periods. The MCP server must rigorously validate that the token is intended for its specific use (audience) and that the requested action is within the token's allowed scope. Establish Verifiable Agent Identities, Each agent should be issued its own distinct, cryptographically verifiable identity (such as a SPIFFE "digital passport"). This allows organizations to apply granular access controls directly to the agent, containing the potential "blast radius" if the agent is compromised or confused. *Keep Secrets Out of the Agent's Context*, API keys, tokens, and other sensitive credentials must be transmitted through secure side channels and kept completely out of the agent's conversational context. This prevents the agent from inadvertently leaking secrets or being tricked into misusing them via a malicious prompt. *Mandate Human-in-the-Loop (HITL)* Approvals: For any high-risk operations—such as modifying production data, deleting files, or pushing code to a repository—the system should treat the action as a sensitive sink. It must pause the workflow and require explicit human confirmation before the MCP server is allowed to execute the command.


#### **Conclusion**
Foundation models, when isolated, are limited to pattern prediction based on their training
data. On their own, they cannot perceive new information or act upon the external world;
tools give them these capabilities. As this paper has detailed, the effectiveness of these
tools depends heavily on deliberate design. Clear documentation is crucial, as it directly
instructs the model . Tools must be designed to represent granular, user-facing tasks, not just
mirror complex internal APIs . Furthermore, providing concise outputs and descriptive error
messages is essential for guiding an agent's reasoning. These design best practices form
the necessary foundation for any reliable and effective agentic system.

The Model Context Protocol (MCP) was introduced as an open standard to manage this
tool interaction, aiming to solve the "N x M" integration problem and foster a reusable
ecosystem. While its ability to dynamically discover tools provides an architectural basis
for more autonomous AI , this potential is accompanied by substantial risks for enterprise
adoption. MCP's decentralized, developer-focused origins mean it does not currently include
enterprise-grade features for security, identity management, and observability. This gap
creates a new threat landscape, including attacks like Dynamic Capability Injection , Tool
Shadowing , and "confused deputy" vulnerabilities.

The future of MCP in the enterprise, therefore, will likely not be its "pure" open-protocol
form but rather a version integrated with layers of centralized governance and control. This
creates an opportunity for platforms that can enforce the security and identity policies not
natively present in MCP. Adopters must implement a multi-layered defense, leveraging API
gateways for policy enforcement , mandating hardened SDKs with explicit allowlists, and
adhering to secure tool design practices. MCP provides the standard for tool interoperability,
but the enterprise bears the responsibility of building the secure, auditable, and reliable
framework required for its operation.