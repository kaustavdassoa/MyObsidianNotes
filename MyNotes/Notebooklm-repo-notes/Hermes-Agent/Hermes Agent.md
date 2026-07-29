## 1. What is Hermes Agent?

Hermes Agent is an open-source, self-improving AI assistant developed by **Nous Research**. Designed to run continuously in the background, it acts as a persistent personal assistant capable of executing actions on your device, server, or cloud.

- **Self-Improving Loop:** It learns from past interactions, builds and refines new "skills" over time, and remembers your preferences across different sessions.
    
- **Persistent Memory:** Instead of wiping context after every chat, it remembers ongoing projects and context via cross-session recall and LLM summarization.
    
- **Contained Sub-Agents:** Spawns isolated, short-lived helper agents for specific sub-tasks to keep the primary workspace clean.
    
- **Model-Agnostic:** Runs on almost any LLM provider (Nous Portal, OpenRouter, OpenAI, Claude, local models via Ollama) and deploys anywhere (local machines, $5 VPS, serverless backends).
    
- **40+ Built-In Tools:** Features integrated web search, browser automation, vision, image generation, and text-to-speech capabilities.
    
- **Multi-Platform Access:** Reachable from a single gateway across 20+ platforms, including CLI, Telegram, Discord, Slack, and WhatsApp.
    

## 2. Hermes Models vs. Hermes Agent

It helps to separate the **model weights** from the **agent software**:

|**Feature**|**Hermes Models**|**Hermes Agent**|
|---|---|---|
|**What is it?**|A family of open-source, fine-tuned Large Language Models (LLMs).|The software framework/orchestrator that executes tasks and manages memory.|
|**Base Tech**|Fine-tuned on base models like Llama (e.g., _Hermes 3_, _Hermes 4_), Mistral, or Qwen.|Can use Hermes models as the default, but works with _any_ LLM (Claude, GPT-4, etc.).|
|**Key Focus**|Specialized for instruction following, function calling, and structured JSON output.|Manages long-term learning, tool execution, and continuous workflows.|

## 3. Similar Alternatives

Hermes Agent occupies a unique niche as an **open-source, self-improving, always-on personal agent**. However, several tools overlap in functionality:

- **OpenClaw (formerly Moltbot/Clawdbot):** The closest direct competitor. Focuses heavily on local machine automation, action-oriented task execution, and direct messaging integrations, whereas Hermes leans more into autonomous optimization and self-learning.
    
- **Vellum:** A local, personal-AI alternative that emphasizes keeping credentials and identities strictly on the user's device rather than a remote server.
    
- **Multi-Agent Frameworks (MetaGPT, CrewAI):** Designed to simulate structured software teams or decompose workflows into defined roles, rather than acting as a single general assistant.
    
- **Managed / Cloud Alternatives:** Commercial, cloud-hosted tools like _Claude Cowork_, _Perplexity Computer_, and _Manus_ offer managed convenience rather than self-hosted control.
    

## 4. How to Install & Run It

_Note: Always cross-reference instructions with the official GitHub repository, as open-source agent frameworks evolve rapidly._

1. **Quick Install (macOS / Linux / Windows via WSL2):**
    
    Run the official installation script in your terminal:
    
    Bash
    
    ```bash
    curl -fsSL https://hermes-agent.nousresearch.com/install.sh | sh
    ```
    
    _(Alternatively, clone the repository via `git clone` if you want to inspect or modify the source code)._
    
2. **Verify the Installation:**
    
    Reload your shell and check dependencies:
    
    Bash
    
    ``` bash
    hermes doctor
    ```
    
3. **Configure a Model Provider:**
    
    Run `hermes` with no arguments to trigger the interactive setup. Choose from providers like **Nous Portal**, direct API keys (Anthropic, OpenAI, etc.), or a **local model via Ollama** (e.g., Qwen 2.5 Coder).
    
4. **Run a Smoke Test:**
    
    Test basic functionality before integrating external tools:
    
    Bash
    
    ```bash
    hermes chat -q "say ready"
    ```
    
5. **Connect Interfaces & Integrations:**
    
    Once the base CLI is working, gradually layer on additional surfaces like Telegram, Discord, GitHub CLI, or a persistent VPS systemd service.
    

## 5. Security Issues & Risks

Because autonomous agents have access to terminal commands, file systems, and external integrations, they introduce significant attack surfaces:

- **Real-World Misuse:** In one reported incident, an attacker deployed Hermes Agent with approval prompts disabled ("YOLO mode") during an intrusion at Thailand's Ministry of Finance and left the logs exposed publicly.
    
- **Unrestricted Shell Access:** Independent audits revealed that local terminal tools can execute bash commands without sandbox restrictions. Malicious instructions hidden in project files (like an altered `AGENTS.md`) could trick the agent into running unauthorized commands.
    
- **Known Vulnerabilities (CVEs):**
    
    - **CVE-2026-11461:** An authorization bypass allowing attackers to access other users' sessions.
        
    - **CVE-2026-7396:** Path traversal vulnerabilities found in specific platform adapters.
        
    - **CVE-2026-53869 & CVE-2026-53870:** DNS rebinding risks on WebSocket endpoints and overly permissive (world-readable) default database permissions.
        
    - **Injection Flaws:** Various context-compression and prompt-injection vectors (mostly patched in versions after `0.12.0`).
        

### **Best Practices for Safe Usage**

- **Never** run the agent with approval prompts disabled ("YOLO mode") on sensitive systems.
    
- **Do not** expose gateway or WebSocket endpoints to the public internet without strict authentication.
    
- **Keep software updated** to ensure patching of known CVEs.
    
- **Be cautious** when pointing the agent at untrusted repositories or shared project directories to prevent prompt-injection attacks.


## **What happened at Thailand's Ministry of Finance was not the result of a "flaw" or vulnerability in Hermes Agent itself.** 

Instead, it is a landmark case of a threat actor using a legitimate, open-source AI agent as an **unattended automation tool** for post-exploitation hacking, and then making a careless operational security (OpSec) mistake that exposed their entire playbook to the public.

Reference Link : https://daily.dev/posts/hermes-ai-agent-used-to-automate-attack-on-thai-finance-ministry-p8yly5m4s

Here is a breakdown of what actually happened, how the agent was used, and how it was discovered:

## 1. How the Attack Went Down

- **The Initial Breach Was Human:** The attacker had _already_ breached the internal network of Thailand's Ministry of Finance before involving the AI. How they originally gained access remains unknown, but they had already planted hidden web shells on ministry servers, stolen mailbox passwords, and targeted internal Hadoop/Hive database clusters.
    
- **Enter Hermes Agent:** To speed up the tedious, repetitive parts of hacking, the attacker installed the open-source **Hermes Agent** onto a rented server and pointed it at the ministry's internal network.
    
- **"YOLO Mode" (Unattended Execution):** Hermes Agent has a documented command-line feature called `YOLO mode`. By default, an AI agent will pause and ask a human for permission before executing potentially dangerous terminal commands. Turning on YOLO mode disables these safety prompts, allowing the AI to run commands autonomously in a continuous loop.
    
- **What the AI Did:** Left to run on its own, Hermes autonomously performed the repetitive "grunt work" of a cyberattack:
    
    - It scanned internal Linux hosts for privilege-escalation paths (using standard scripts like `LinPEAS`).
        
    - It swept the network for kernel vulnerabilities and binaries with elevated permissions.
        
    - It crawled internal file systems and cataloged sensitive folders—including an Office of the Permanent Secretary directory containing staff personnel records and performance evaluations dating back to 2012.
        

## 2. The "Publicly Exposed Logs" Blunder

The reason the cybersecurity world knows so much about this attack is due to a massive operational mistake by the hacker, not an AI bug.

- **Open Directory Listing:** The threat actor left their own rented web server configured with **directory listing turned on**. Anyone who navigated to the server's IP address could view and download every file sitting on it.
    
- **What Researchers Found:** Threat intelligence firm **Hunt.io** and researcher Bob Diachenko discovered the open server. Sitting out in the open were **585 files (470 MB of attack tooling)**, including:
    
    - A previously undocumented Go-based malware implant dubbed _"Hades"_.
        
    - Custom scripts designed to execute Remote Code Execution (RCE) on the ministry's Hadoop SQL servers.
        
    - **The Hermes Output Logs:** Most importantly, researchers found five detailed log files (`call_00_*.txt`) and a `/hermes-results/` folder. Because LLM agents record their prompt history, "thoughts," and terminal outputs, these logs provided a step-by-step transcript of everything the AI did inside the ministry's network.
        

## 3. Why This Incident is Significant

- **No Vendor Kill-Switch:** In previous AI-assisted cyberattacks—such as state-sponsored groups trying to use Anthropic's Claude Code—the AI company eventually noticed suspicious prompts and banned the accounts. Because Hermes Agent is open-source software that runs locally on the user's own machine/API endpoints, **there was no vendor watching and no account to ban.**
    
- **A New Era of "Grunt Work" Automation:** The AI didn't invent new exploits or magically break into the ministry. Instead, it acted like a relentless junior analyst: running a scan, reading the output, deciding what to check next, and repeating the process at machine speed without human fatigue.
    
- **A Warning on AI Footprints:** Hunt.io noted that default Hermes installations write results to predictable folders (`/hermes-results/`) and emit recognizable server headers (`HermesWebUI`). When researchers scanned the broader internet for those specific indicators, they found over **570 other exposed directories** hosting Hermes agent outputs—proving that many users (both legitimate developers and threat actors) are carelessly leaving their AI logs exposed online.

