
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

**Google Model Armor**
1. A user provides a prompt to the application.
2. Model Armor inspects the incoming prompt for potentially sensitive  content.
3. The prompt (or sanitized prompt) is sent to the LLM.
4. The LLM generates a response.
5. Model Armor inspects the generated response for potentially sensitive content.
6. The response (or sanitized response) is sent to the user. Model Armor sends a detailed description of triggered and untriggered filters in the response.

**Managing long context conversation: tradeoffs and optimizations**

Agents determine when session history compaction is necessary using specific **trigger mechanisms** that signal it is time to condense the conversation log. Because sophisticated compaction strategies, such as using an LLM to generate recursive summaries, can be computationally expensive and time-consuming, they must be executed thoughtfully—typically as an asynchronous background process so the user is not kept waiting.

To manage this, agents generally rely on **three distinct categories** of triggers to decide when compaction is necessary:

- **Count-Based Triggers:** This is a straightforward threshold approach where the agent initiates compaction once the conversation surpasses a predefined limit, such as a **specific token size or turn count threshold**. This method is often considered "good enough" for routinely managing an LLM's context length constraints.
- **Time-Based Triggers:** Instead of measuring the size of the conversation, this mechanism monitors for a **lack of activity**. If a user stops interacting with the agent for a set period of time—such as 15 or 30 minutes—the system will automatically run a compaction job in the background while the agent is idle.
- **Event-Based Triggers (Semantic/Task Completion):** In this more context-aware approach, the agent dynamically decides to trigger compaction when it detects that a **specific task, sub-goal, or topic of conversation has naturally concluded**.

By relying on these triggers, agents can effectively condense verbose dialogue into key facts and summaries, ensuring they carry only the most essential information forward without slowing down the immediate user experience.

## **Memory**

### Benefit of robust memory management 

A robust memory system transforms a basic chatbot into a sophisticated, intelligent agent by unlocking several key benefits:

- **Personalization:** Memory allows an agent to remember user preferences, specific facts, and past interactions to tailor its future responses. For example, recalling a user's preferred airline seat or tracking the outcome of a task from weeks ago creates a highly personalized and continuous user experience.
- **Context Window Management:** As conversations grow, the full dialogue history can easily exceed a Language Model's token limits. Memory systems help compact this history by extracting key facts and generating summaries, which preserves the necessary context without sending thousands of tokens per turn, ultimately reducing both operational cost and latency.
- **Data Mining and Insight:** By aggregating and analyzing stored memories across multiple users in a privacy-preserving manner, organizations can extract valuable insights from conversational noise. For instance, a retail agent might detect a pattern of users asking about a specific product's return policy, automatically flagging a potential business issue.
- **Agent Self-Improvement and Adaptation:** An agent can learn from its previous executions by generating "procedural memories" about its own performance. By recording which reasoning paths, tools, or strategies resulted in successful outcomes, the agent builds a reusable "playbook" that allows it to autonomously adapt and improve its problem-solving capabilities over time.

## The roles of the different components in managing memory effectively 

Creating, storing, and utilizing memory in an AI agent system is a highly collaborative process where each layer of the technology stack performs a specific, vital function. The effective management of memory relies on the interplay of the following core components:

- **The User:** The user acts as the origin point by providing the raw source data that forms the basis of memories. This data is usually captured implicitly through natural conversation, though in some systems, users might provide memories directly via explicit inputs like a form.
- **The Agent (Developer Logic):** This component is responsible for orchestrating calls to the memory manager and determining **what and when to remember**. Developers can hardcode this logic so that memory is always retrieved and generated on every turn, or they can implement more advanced "memory-as-a-tool" patterns where the agent's LLM is empowered to autonomously decide when it needs to retrieve or generate memory.
- **The Agent Framework (e.g., ADK, LangGraph):** Acting as the system's "plumbing," the framework provides the necessary structure and tools for memory interaction. It dictates how the developer's logic accesses the conversation history and interacts with the memory manager. Crucially, the framework itself does not manage long-term storage; instead, it defines how retrieved memories are appropriately formatted and "stuffed" into the LLM's context window for inference.
- **The Session Storage (e.g., Agent Engine Sessions, Spanner, Redis):** This component is responsible for storing the chronological, turn-by-turn events of the current session. This raw dialogue acts as the foundational data source that will eventually be ingested by the memory manager to generate persistent memories.
- **The Memory Manager (e.g., Agent Engine Memory Bank, Mem0, Zep):** This is the specialized, active service that handles the entire lifecycle of a memory once the source data is provided. Instead of just passively storing data, the memory manager handles the complex tasks of * *  
	 - **extraction** (distilling key information from the noise),
	 - **consolidation** (curating and merging duplicative entities to prevent contradictory facts), 
	 - **storage** (saving the processed memory to a persistent database), 
	 - **retrieval** (fetching the most relevant memories to provide context for new interactions).

**RAG Engines Vs Memory Managers**
The memory and RAG have two district, complementary role to play : RAG makes
an agent an expert on facts, while memory makes it an expert on the user  

|Feature|RAG Engines|Memory Managers|
|---|---|---|
|**Primary Goal**|To inject external, factual knowledge into the context.|To create a personalized and stateful experience, allowing the agent to remember facts, adapt to the user over time, and maintain long-running context.|
|**Data Source**|A static, pre-indexed external knowledge base (e.g., PDFs, wikis, documents, APIs).|The dialogue between the user and agent.|
|**Isolation Level**|**Generally Shared:** The knowledge base is typically a global, read-only resource accessible by all users to ensure consistent, factual answers.|**Highly Isolated:** Memory is almost always scoped per-user to prevent data leaks.|
|**Information Type**|Static, factual, and authoritative, often containing domain-specific data, product details, or technical documentation.|Dynamic and generally user-specific, derived from conversation, which inherently carries some level of uncertainty.|
|**Write Patterns**|**Batch processing:** Triggered via an offline, administrative action.|**Event-based processing:** Triggered at a specific cadence (e.g., every turn or at session end) or via "Memory-as-a-tool" where the agent decides to generate memories.|
|**Read Patterns**|Almost always retrieved **"as-a-tool"** when the agent decides the user's query requires external information.|Two common patterns: **Memory-as-a-tool** (when the user's query requires additional info about the user) or **Static retrieval** (retrieved automatically at the start of each turn).|
|**Data Format**|A natural-language "chunk".|A natural language snippet or a structured profile.|
|**Data Preparation**|**Chunking and Indexing:** Source documents are broken into smaller chunks, converted to embeddings, and stored for fast lookup.|**Extraction and consolidation:** Key details are extracted from the conversation, ensuring the content is not duplicative or contradictory.|
###  Types of Memory

An AI agent's memory can be categorised in several different ways, depending on the cognitive function of the information, its structural format, how it was created, its scope, and its modality.

Here is a breakdown of the different types of memories:

**1. By Type of Information (Cognitive Function)** Drawing from cognitive science, memory is split into two primary functional categories:

- **Declarative memory ("knowing what"):** This encompasses the facts, figures, and events that the agent can explicitly state. It includes both general world knowledge (Semantic memory) and specific facts about the user (Entity/Episodic memory).
- **Procedural memory ("knowing how"):** This represents the agent's knowledge of skills, workflows, and strategies. It acts as a "playbook" that guides the agent's actions by demonstrating the correct sequence of steps or tool calls required to complete a task successfully.

**2. By Content Structure** The substance of a memory can be extracted and stored in different formats:

- **Structured memories:** Information stored in universal, developer-defined formats such as JSON or dictionaries (e.g., `{"seat_preference": "Window"}`).
- **Unstructured memories:** Natural language descriptions that summarise the essence of a longer interaction, event, or topic (e.g., "The user prefers a window seat.").

**3. By Creation Mechanism** Memories can be classified by how the agent acquired the information:

- **Explicit memories:** Created when a user gives the agent a direct, explicit command to remember a fact (e.g., "Remember my anniversary is October 26th").
- **Implicit memories:** Created when the agent autonomously infers and extracts meaningful information from the natural flow of the conversation without being directly instructed to do so.

**4. By Scope (Who or what the memory describes)** Memories are defined by the entity they are tied to, which dictates how they are aggregated and retrieved:

- **User-Level scope:** Memories linked to a specific user ID that persist across all of their sessions. This is the most common implementation, allowing the agent to build a long-term understanding of a user's preferences.
- **Session-Level scope:** Memories isolated to a specific session. This scope is used to compact long, verbose conversations into a concise set of key facts for that specific interaction.
- **Application-Level scope (Global):** Memories that are accessible to all users of an application. This is commonly used to establish shared context, broadcast system-wide information, or store shared procedural memories (provided they are strictly sanitised of personal data).

**5. By Modality (Multimodal Memory)** This describes how the agent handles non-textual information, distinguishing between the source of the memory and its final stored format:

- **Memory from a multimodal source:** The agent processes non-textual data, such as transcribing a voice memo, but stores the extracted insight purely as text. This is the most common approach for contemporary memory managers.
- **Memory with Multimodal Content:** A more advanced method where the non-textual media itself (such as an uploaded image file) is stored directly within the memory.

**6. By Management Location** Finally, memories can be distinguished by where the logic resides:

- **Internal Memory:** Memory management built directly into the agent framework.
- **External Memory:** Memory management offloaded to a separate, specialised service (like Agent Engine Memory Bank or Zep) which provides sophisticated features like automatic summarisation and entity extraction.


### Memory Creation 

Memories can be classified by the mechanisms through which they are created and how the agent acquires the information. The primary creation mechanisms based on how information is derived are:

- **Explicit memories:** These are created when a user issues a direct command instructing the agent to remember a specific fact, such as explicitly telling it to "Remember my anniversary is October 26th".
- **Implicit memories:** These are generated when the agent autonomously infers and extracts meaningful information from the natural flow of the conversation, without receiving a direct command to do so.

Furthermore, the mechanisms for creating these memories can be distinguished by where the memory extraction logic resides:

- **Internal Memory:** The mechanism for generating memories is built directly into the agent framework itself. While this is convenient for getting started, it often lacks more advanced features.
- **External Memory:** The agent framework makes API calls to a separate, specialised service (such as Agent Engine Memory Bank, Mem0, or Zep) which is dedicated entirely to managing, storing, retrieving, and processing memories.

Finally, agents rely on specific **triggering mechanisms** to determine exactly _when_ the memory generation process should be initiated. These triggers include:

- **Session Completion:** Running the generation process at the end of a multi-turn session.
- **Turn Cadence:** Generating memories after a specific number of turns.
- **Real-Time:** Extracting memories after every single turn.
- **Explicit Command:** Activating the process upon a direct user command.

More advanced architectures also employ **Memory-as-a-Tool**, where the agent is equipped with a tool that allows it to analyse the conversation and autonomously decide for itself when to trigger memory generation.

The confidence in a memory must evolve. Confidence increases through corroboration, such as when a multiple trusted sources providing consistent information. However, an effective memory system must also actively curate its existing knowledge though pruning - a process the delete/forget memory that are no longer needed. The pruning can be trigger by several factors like :
- **Time based decay**
- **Low confidence** 
- **Irrelevance** 

An agent memory can't be static it needs to be evolving - the following are the several process through which memory generation can be triggered.

- **Session Completion** 
- **Real-time memory generation** - after each call.
- **Explicit Command** - use activated the generation process (example : user mention remember this : my birthday is on 25th May.) 

However, the choice of the trigger involves balancing the trade-offs between - Frequency Vs Cost. 

### **Memory-as-a-tool.** 

