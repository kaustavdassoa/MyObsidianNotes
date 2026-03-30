
The **"Context Engineering: Sessions, Memory"** whitepaper explores how developers can build stateful, intelligent LLM agents by dynamically managing the information provided to the model. Here is a summary of the core topics:

**1. Context Engineering** Because LLMs are inherently stateless, their reasoning is confined to the information provided in a single API call's "context window". **Context engineering** is the dynamic assembly of this information, moving beyond static prompt engineering to build a state-aware payload that includes system instructions, tools, retrieved knowledge (like RAG or memory), and conversation history. This operates in a continuous loop: fetching context, preparing the prompt, invoking the LLM/tools, and uploading new context to storage.

**2. Sessions (Working Memory)** A **Session** encapsulates the immediate, turn-by-turn dialogue history and temporary working memory (state) for a single conversation tied to a specific user.

- **Compaction Strategies:** As conversations grow, token limits, latency, and costs increase. To manage this, agents employ compaction strategies such as keeping only the last _N_ turns, token-based truncation, or using an LLM to generate recursive summaries of older messages.
- **Production Considerations:** In production, sessions require strict isolation (ACLs) to prevent data leaks between users, automated redaction of Personally Identifiable Information (PII), and Time-to-Live (TTL) policies for lifecycle management.

**3. Memory (Long-Term Persistence)** **Memory** captures and consolidates extracted information across multiple sessions, allowing the agent to personalize interactions, adapt over time, and manage context windows.

- **Memory vs. RAG:** While Retrieval-Augmented Generation (RAG) acts as a "research librarian" fetching static, authoritative facts from a shared database, a Memory Manager acts as a "personal assistant," managing highly isolated, dynamic insights specifically about the user.
- **Types of Memory:** Information is categorized into **declarative memory** (facts and events, or "knowing what") and **procedural memory** (workflows and playbooks, or "knowing how").
- **Storage & Organization:** Memories are commonly organized as collections, structured user profiles, or rolling summaries. They are stored in vector databases (for semantic similarity) or knowledge graphs (for structured, relational reasoning), or a hybrid of both.

**4. Memory Generation: Extraction and Consolidation** Generating memory is an LLM-driven ETL (Extract, Transform, Load) pipeline.

- **Extraction:** The system filters out conversational noise to extract only predefined, meaningful insights (using templates, schemas, or few-shot examples).
- **Consolidation:** The system "self-edits" by comparing new insights against existing memories. It can merge, update, or delete older memories to resolve conflicts, remove duplicates, and ensure the knowledge base evolves accurately.
- **Provenance:** The system tracks memory lineage (its source and age) to establish a hierarchy of trust. This helps the agent dynamically adjust its confidence score during inference and enables safe memory pruning/forgetting over time.

**5. Retrieval and Inference**

- **Retrieval Strategies:** Finding the right memory involves scoring candidates across semantic relevance, recency, and overall importance. Agents can retrieve memories proactively (at the start of every turn) or reactively via "Memory-as-a-Tool," where the agent autonomously decides when it needs to fetch background information.
- **Inference Placement:** Retrieved memories must be strategically placed in the prompt. "Global" stable memories (like a user profile) are best placed in the system instructions, whereas transient, episodic memories are better injected directly into the conversation history or tool outputs.

**6. Testing, Evaluation, and Production** Evaluating memory requires testing precision and recall for generation (did it remember the right things?), latency and Recall@K for retrieval (did it find them fast?), and end-to-end task success. In a production architecture, memory generation must be decoupled from the main application as a non-blocking, asynchronous background process (using message queues) so users do not experience latency while the LLM extracts and consolidates data. Furthermore, safeguards like input/output sanitization are mandatory to protect against "memory poisoning" (prompt injection) and PII leakage.

___
Additional Notes 

*"Stateful and personal AI begins with Context Engineering."* 

### **Context Engineering**

LLMs are inherently stateless. Outside of their training data, their reasoning and awareness
are confined to the information provided within the "context window" of a single API call.
This presents a fundamental problem, as AI agents must be equipped with operating
instructions identifying what actions can be taken, the evidential and factual data to reason
over, and the immediate conversational information that defines the current task. To build
stateful, intelligent agents that can remember, learn, and personalize interactions, developers
must construct this context for every turn of a conversation. This dynamic assembly and
management of information for an LLM is known as Context Engineering.

Context Engineering represents the evolution of the Prompt Engineering. Context Engineering addresses the entire payload, dynamically constructing a state-aware prompt based on the user, conversation history, and external data. It involves strategically selecting, summarizing, and injecting different types of information to maximize relevance while minimizing noise. External systems—such as RAG databases, session stores, and memory managers—manage much of this context. 

Context Engineering governs the assembly of a complex payload that can include a variety
of components:

![](../../../../Notes/ZAttachments/Pasted%20image%2020260328142501.png)

Context to guide reasoning defines the agent’s fundamental reasoning patterns and
available actions, dictating its behavior:

*  **System Instructions**: High-level directives defining the agent's persona, capabilities,
   and constraints.
* **Tool Definitions**: Schemas for APIs or functions the agent can use to interact with the
	outside world.
* **Few-Shot Examples**: Curated examples that guide the model's reasoning process via
  in-context learning.

* **Evidential & Factual Data** is the substantive data the agent reasons over, including preexisting
  knowledge and dynamically retrieved information for the specific task; it serves as
  the 'evidence' for the agent's response:

* **Long-Term Memory**: Persisted knowledge about the user or topic, gathered across
  multiple sessions.
* **External Knowledge**: Information retrieved from databases or documents, often using RAG.
* **Tool Outputs**: The data or results returned by a tool.
* **Sub-Agent Outputs**: The conclusions or results returned by specialized agents that
  have been delegated a specific sub-task.
* **Artifacts**: Non-textual data (e.g., files, images) associated with the user or session.

* **Immediate conversational** information grounds the agent in the current interaction,
  defining the immediate task:
	* **Conversation History**: The turn-by-turn record of the current interaction.
	* **State / Scratchpad**: Temporary, in-progress information or calculations the agent uses
      for its immediate reasoning process.
	* **User's Prompt**: The immediate query to be addressed.

"**context rot**" a phenomenon where their ability to pay attention to critical information diminishes as context grows.

#### **Core components of Context Engineering** 

There are two fundamental components of context engineering

* A **Session** manages the turn-by-turn state of a single conversation.
* **Memory**, in contrast, provides the mechanism for long-term persistence, capturing and consolidating key information across multiple sessions.

#### **Session** 

The session allows the agent to maintain context and provide coherent responses within the bounds of a single conversation.

**Every session contains two key components** :

* **Event** : the chronological history.
* **State** : Agent's working memory.

**Different types of events :**

* **User Inputs** : A message from the user (text, audio, image, etc.)
* **Agent Response** : The agent's reply to the user
* **Tool Call** : The agent’s decision to use an external tool or API
* **Tool Response** :  The data returned from a tool call, which the agent uses to continue its reasoning.

Session often includes a state—a structured "working memory" of an agent. 


![](../../../../Notes/ZAttachments/Pasted%20image%2020260328151345.png)


**Variance across frameworks and models :**

**ADK** uses an explicit Session object that contains a list of Event objects and a separate
state object. The Session is like a filing cabinet, with one folder for the conversation history
(events) and another for working memory (state).

**LangGraph** doesn't have a formal "session" object. Instead, the state is the session. This all-encompassing state object holds the conversation history (as a list of Message objects) and
all other working data. Unlike the append-only log of a traditional session, LangGraph's state
is mutable. It can be transformed, and strategies like history compaction can alter the record.
This is useful for managing long conversations and token limits.

**Sessions for multi-agent systems :**

A central component of a multi-agent  architecture is how the system handles session history—the persistent log of all interactions.

Agent frameworks handle session history for multi-agent systems using one of two primary
approaches: **a shared**, unified history where all agents contribute to a single log, or **separate**,
individual histories where each agent maintains its own perspective. 

This interaction is typically implemented by either implementing **Agent-as-a-tool** or using the
**Agent-to-Agent (A2A)** Protocol. With **Agent-as a-Tool**, one agent invokes another as if it were
a standard tool, passing inputs and receiving a final, self-contained output. With the **Agent-to-**
**Agent (A2A)** Protocol, agents use a structured protocol for direct messaging.

In both the **Agent-as-a-Tool** and the **Agent-to-Agent (A2A)** approaches, the entire session of the root agent is **not** passed to the called agent. Both of these approaches use a **separate, individual histories model**.


![](../../../../Notes/ZAttachments/Pasted%20image%2020260328190033.png)
Interoperability across multiple agent frameworks

A framework's use of an internal data representation introduces a critical architectural
trade-off for multi-agent system: With higher degree of abstraction - decouples an agent from an
LLM also isolates it from agents using other agent frameworks.  The storage model for a Session typically couples the database schema directly to the framework's internal objects, creating a rigid, relatively non-portable conversation record.  *Therefore, an agent built with LangGraph cannot natively interpret the distinct Session and Event objects persisted by an ADK-based agent, making seamless task handoffs impossible.*

Each agent's conversation history is encoded in its framework's internal schema. As a result, any A2A message containing session events requires a translation layer to be useful. The memory layer’s data structures are not coupled to any single framework's internal data representation, which allows it to serve as a universal, common data layer. This pattern allows heterogeneous agents to achieve true collaborative intelligence by sharing a common cognitive resource without requiring custom translators.

When moving an agent to production - the flowing are the three things that one need to keep in mind when considering Context Engineering :
- Security 
- Privacy 
- Data Integrity 
- Performance 

**Security & Privacy** 
- There should be a strict isolation enforced between the user's session data , using ACLs.
- For handling Personally Identifiable Information (PII) is to redact it before the
  session data is ever written to storage. Tools like Google Model Armor can be leveraged for this - "*Model Armor is a Google Cloud service designed to enhance the security and safety of your AI applications. It works by proactively screening LLM prompts and responses, protecting against various risks and ensuring responsible AI practices.*".
- Regulations like GDPR and CCPA also needs to be keep in mind. 

```python 

"""
	    1. A user provides a prompt to the application.
		2. Model Armor inspects the incoming prompt for potentially sensitive  content.
		3. The prompt (or sanitized prompt) is sent to the LLM.
		4. The LLM generates a response.
		5. Model Armor inspects the generated response for potentially sensitive content.
		6. The response (or sanitized response) is sent to the user. Model Armor sends a detailed description of triggered and untriggered filters in the response.

"""
```		


Page # 21