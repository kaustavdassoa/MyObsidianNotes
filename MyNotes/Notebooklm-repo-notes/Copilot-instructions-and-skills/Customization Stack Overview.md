### **The Customization Stack Overview**

GitHub Copilot's extensibility model is built on four core pillars: **Instructions**, **Skills**, **Agents**, and **Hooks**. Together, they transform Copilot from an inline autocomplete utility into an autonomous, context-aware workflow engine capable of reading environments, executing scripts, and enforcing organizational governance.

### **1. Copilot Instructions: The Baseline Guardrails**

**Instructions** are always-on rules that govern Copilot's behavior across a repository or organization.

- **Core Concept:** They act as foundational system prompts that Copilot automatically reads and applies as baseline context for every interaction.
    
- **Best Used For:** Defining project-specific coding standards, language conventions, framework choices, and code review guidelines.
    
- **Implementation:** Typically configured globally in organizational settings or locally via `.github/copilot-instructions.md`. Copilot can dynamically apply specific rules based on file types using glob patterns.
    
- **Design Rule:** Use Instructions when you want Copilot to behave consistently _all the time_.
    

### **2. Copilot Agent Skills: On-Demand Workflows**

**Skills** are reusable, multi-step procedures designed for domain-specific tasks. Unlike Instructions, they are actively managed via an open standard and support bundled assets.

- **Core Concept:** Skills act as intelligent runbooks. They prevent context bloat by utilizing **progressive loading**. Copilot reads only the name and description of available skills (costing ~100 tokens); it loads the full skill body and any associated reference files only when the skill is matched or directly invoked.
    
- **Structure:** Skills reside in dedicated directories (e.g., `.github/skills/<skill-name>/SKILL.md`). The folder can also contain secondary resources like helper scripts, templates, and short reference documents.
    
- **Frontmatter & Discovery:** The `SKILL.md` file requires YAML frontmatter containing the skill's `name` and `description`. Because "description is discovery," keywords in the description dictate when the Agent autonomously loads the skill based on user intent.
    
- **Invocation:**
    
    - **Automatic:** The Agent detects user intent in a prompt and matches it to a skill's description.
        
    - **Manual:** Users can invoke a skill directly via a chat slash command (e.g., `/webapp-testing`).
        

### **3. Copilot Agents: The Autonomous Execution Engine**

The **Agent** (operating in Agent Mode) is the orchestration layer that leverages the Microsoft Agent Framework to execute complex tasks.

- **The Agent Loop:** Instead of simple prompt-response cycles, Agents utilize a continuous loop of reasoning: researching, planning, iterating, and managing tools.
    
- **Capabilities:** An Agent can autonomously invoke a Skill, navigate workspaces, read endpoints, run shell scripts, and synthesize the output.
    
- **Extensibility:** Agents can securely connect to external enterprise tools (like live databases or third-party APIs) via the **Model Context Protocol (MCP)**, functioning seamlessly across IDEs and the Copilot CLI.
    

### **4. Copilot Hooks: Lifecycle Interceptors**

**Hooks** provide event-driven extensibility, allowing developers to trigger automated actions during specific phases of a Copilot Agent session.

- **Core Concept:** Hooks intercept the Agent Loop at runtime to enforce safety constraints, inject live context, or log telemetry before or after an action is taken.
    
- **Event Lifecycle States**:
    
    - `User Prompt Submitted`: Captures the initial user input to modify or log the request.
        
    - `Pre Tool Use`: Triggers right before an Agent executes a tool or command (ideal for security checks or required approvals).
        
    - `Post Tool Use`: Triggers after execution (ideal for parsing output or formatting results).
        
    - `Session Lifecycle`: Manages state when sessions start or end.
        
    - `Error Handling`: Intercepts and formats error responses.
        

**Summary of Interaction**

When a developer asks Copilot to _"check deployment health"_:

1. The **Agent** loop initiates and applies global **Instructions** to ensure compliance.
    
2. The Agent matches the prompt against the frontmatter description of the `deployment-health` **Skill** and progressively loads its procedure.
    
3. Right before running the skill's bundled Python script, a `Pre Tool Use` **Hook** might trigger to verify the developer has the necessary permissions.
    
4. The Agent executes the script, analyzes the output, and returns a formatted markdown summary directly into the chat interface.
    
