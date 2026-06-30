# Intermediate Study Guide: Hermes-Agent Function Calling & Prompt Formatting

This study guide is designed to elevate your understanding of the Hermes framework from basic concepts to a functional mastery of tool calling, prompt syntax, and agentic workflows. 

***

## 1. Concise Overview of the Topic
In the Hermes framework, an agent is an instruct-tuned language model transformed into a decision-maker capable of interacting with external environments. This is achieved through **Function Calling** (or Tool Calling), where the model leverages specialized prompt formatting, XML tags, and JSON schemas to request out-of-band computations or data retrievals. By mastering the specific ChatML prompt structure and Goal Oriented Action Planning (GOAP) tags, developers can seamlessly integrate Hermes models into complex orchestration pipelines like LangChain or custom applications.

***

## 2. Key Concepts & Definitions

*   **ChatML Format**: The foundational prompt format used by Hermes models (like Hermes 2 Pro). It uses special tokens like `<|im_start|>assistant\n` to denote the beginning and end of conversational turns and assign specific roles (system, user, assistant, tool). This enables multi-turn dialogue and OpenAI endpoint compatibility.
*   **System Prompts**: A critical meta-command given to the model that establishes its persona, rules, and stylistic choices. Hermes models are highly sensitive to these prompts, and they are where tool definitions and schemas are injected.
*   **Tool Calling (Function Calling)**: The process where the AI decides to invoke an external function to assist with a user query. In Hermes, the available functions are defined using JSON schemas placed inside `<tools>` XML tags in the system prompt. 
*   **JSON Mode (Structured Outputs)**: A specialized operating mode where the model is prompted with a specific JSON schema (often generated via a Pydantic object) and is restricted to responding *only* with a valid JSON object adhering to that exact schema.
*   **GOAP (Goal Oriented Action Planning)**: An internal reasoning framework that can be enabled within the model's `<scratch_pad>`. It allows the model to explicitly restate goals, plan actions using Python-style function calls, observe tool results, and evaluate task status before generating a final response.

***

## 3. Prompt Structures & Practical Syntax Examples

To build a Hermes agent, you must understand how to format the input and how the model formats its output.

### Defining Tools in the System Prompt
To enable function calling, the system prompt must tell the model it is a function-calling AI and provide the available function signatures.
*   **Syntax**: Tool definitions are provided as JSON schemas wrapped in `<tools>` XML tags. *(Note: The exact structure of the JSON schema payload is not explicitly detailed in the text, but the sources indicate they are generated using Pydantic models via scripts like `schema.py`).*

### Executing a Tool Call (Model Output)
When the model decides a tool is needed, it outputs an XML tag containing a JSON object with the function's name and arguments.
*   **Syntax Snippet**: 
    `<tool_call> {"arguments": {"confirmation_required": true}, "name": "lock_all_smart_doors"} </tool_call>`
*   **Example 2**: 
    `<tool_call> {'arguments': {'device_id': 'smart_plug_123', 'on_time': '19:00', 'off_time': '23:00', 'repeat_daily': True}, 'name': 'schedule_smart_plug'} </tool_call>`

### Returning Tool Data to the Model
Once the system parses the `<tool_call>` and executes the external function, the returned data must be passed back to the model as a new role (the `tool` role), enclosed in specific tags.
*   **Syntax**: The response is appended using `<tool_response>` tags so the model can read the data and generate a natural language answer.

### Advanced GOAP Prompt Formatting
For complex, multi-step problem solving (like generating code), Hermes 3 utilizes reserved tokens to generate a structured internal monologue. A system prompt can enforce the following nested structure:
1.  `<SCRATCHPAD>`: Opens the planning phase.
2.  `<RESTATEMENT>`: Restates the user's problem.
3.  `<REASONING>`: Contains individual insights wrapped in `<THOUGHT_N>` tags.
4.  `<PLAN>`: A step-by-step plan utilizing `<STEP_N>` tags.
5.  `<PYDANTIC_SCHEMAS>`: Defines required objects using `<SCHEMA_N>`.
6.  `<DIAGRAM>`: Generates a UML workflow diagram.
7.  `<REFLECTION>`: An internal critique of the plan to catch blindspots.
8.  `<SOLUTION>` & `<EXPLANATION>`: The actual code and explanation after closing the scratchpad.

***

## 4. Visualizing the Tool-Calling Cycle

| Phase | Actor | Action & Formatting |
| :--- | :--- | :--- |
| **1. Setup** | Developer | Submits a ChatML prompt with a system message containing function JSON schemas inside `<tools>` tags. |
| **2. Request** | User | Submits a natural language query (e.g., "Set the thermostat to 72°F"). |
| **3. Reasoning** | Hermes Agent | (Optional) Uses `<scratch_pad>` to process GOAP logic and plan the execution. |
| **4. Invocation** | Hermes Agent | Outputs a structured invocation: `<tool_call> {"arguments": {...}, "name": "..."} </tool_call>`. |
| **5. Execution** | External System | Parses the tool call, executes the code, and returns the result. |
| **6. Observation** | Developer | Injects the external system's result back into the prompt within `<tool_response>` tags under a `tool` role. |
| **7. Final Answer**| Hermes Agent | Reads the `<tool_response>` and generates a conversational natural language reply to the user. |

***

## 5. Connections Across Sources

The provided sources represent a complete pipeline for building an agent:
*   **The Blueprint**: The *NousResearch GitHub* (Source 4) and *Hermes 3 Technical Report* (Source 7) define the architectural rules—how ChatML works, how GOAP tags (`<scratch_pad>`) operate, and how tool schemas are formatted.
*   **The Training Ground**: The *Hugging Face Dataset* (Source 1) provides the raw materials. It contains ~1,900 stratified examples (like quantum computing or smart home controls) formatted perfectly with `<tool_call>` tags, showing how Hermes learned this behavior.
*   **The Deployment Strategy**: *TRL on Azure* (Source 2) and *LangChain* (Source 3) show how to put the model into production. Azure provides the infrastructure to fine-tune models on datasets like Source 1, while LangChain provides orchestration tools like `bind_tools()` to automatically translate Python/Pydantic schemas into the XML/JSON formats Hermes expects.

***

## 6. Summary of Key Sources

*   **Hermes-Function-Calling GitHub Repository**: Contains the reference code and documentation for utilizing Hermes Pro. It introduces `jsonmode.py` for structured outputs, `schema.py` for Pydantic validation, and explains the ChatML and XML formatting required for inference. *(Note: The actual Python code inside files like `jsonmode.py` is not visible in the sources, only their descriptions)*.
*   **Hermes 3 Technical Report**: Details the model's training on Llama 3.1 architecture, its 128K context window, and its agentic features. Crucially, it lists all the supported XML tags for reasoning (`<INNER_MONOLOGUE>`, `<REFLECTION>`, etc.) and explains its neutral alignment.
*   **LangChain ChatPredictionGuard Docs**: Explains how to integrate secure generation into LangChain using `ChatPredictionGuard.bind_tools()`. It maps model-agnostic `AIMessage.tool_calls` formats into system-specific executions.
*   **Hugging Face TRL on Azure Guide**: A tutorial on supervised fine-tuning (SFT) of a 2B parameter model using the `hermes-function-calling-v1` dataset so it can reliably invoke tools over an API.
*   **Hugging Face Dataset (Bharatdeep-H)**: Raw conversational logs of training data showing the exact system prompts and JSON arguments required to trigger tools across various industries (IoT, Quantum, ERP).

***

## 7. Practice Questions & Answers

**Q1: If you want Hermes to return *only* a JSON object that matches a specific structure, what feature do you use and how do you configure the prompt?**
**Answer:** You use JSON Mode (Structured Outputs). You must provide a specific system prompt containing the desired `{schema}` (usually generated from a Pydantic object) and instruct the model to respond exclusively in that JSON format.

**Q2: What prompt formatting standard does Hermes use to assign roles like "system", "user", and "assistant"?**
**Answer:** Hermes uses the ChatML format, which relies on special tokens like `<|im_start|>assistant\n` to organize the conversation. 

**Q3: Describe the exact syntax Hermes uses when it decides to call an external tool.**
**Answer:** It outputs an XML tag containing a JSON object with the arguments and function name: `<tool_call> {"arguments": {"key": "value"}, "name": "function_name"} </tool_call>`.

**Q4: In the GOAP (Goal Oriented Action Planning) framework, what XML tag does the model use to evaluate if it has any blindspots before providing a final solution?**
**Answer:** The `<REFLECTION>` tag.

***

## 8. Revision Checklist & Key Takeaways

Use this checklist to ensure you are ready to build or test Hermes Agent workflows:

*   [ ] **Understand ChatML:** I know how to structure prompts using ChatML tags and inject system instructions.
*   [ ] **Define Tools:** I understand that tools are defined as JSON signatures and injected into the system prompt inside `<tools>` tags.
*   [ ] **Parse Invocations:** I can write code to parse the `<tool_call>` XML tags and extract the nested JSON `name` and `arguments`.
*   [ ] **Handle Responses:** I know to append external API results back to the model within `<tool_response>` tags under the `tool` role.
*   [ ] **Utilize GOAP:** I am familiar with the internal scratchpad workflow and can format a prompt to require `<REASONING>`, `<PLAN>`, and `<REFLECTION>` tags.
*   [ ] **Differentiate JSON Mode:** I understand the difference between letting a model pick a tool (Function Calling) versus forcing the model to output a singular Pydantic-validated JSON payload (JSON Mode).