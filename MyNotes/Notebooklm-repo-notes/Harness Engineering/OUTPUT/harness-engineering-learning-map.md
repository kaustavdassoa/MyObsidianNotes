# Harness Engineering Learning Map
### From "I use an AI agent" to "I engineer the system around it"

> **Core formula to keep in mind throughout:** `Agent = Model + Harness`
> The model gives you reasoning. The harness gives you reliability, safety, and repeatability.

---

## How to use this map

The map has **5 phases**, each with: what to learn, why it matters, hands-on exercises, and signals you're ready to move on. It's designed to be worked through sequentially, but Phases 3–5 are iterative — you'll revisit them as your agent grows.

Rough time budget for a working developer: **6–10 weeks** at a few hours a day, or **2–3 weeks** if focused full-time.

---

## Phase 0 — Foundations (Prerequisites)

Before harness engineering makes sense, you need the mental model of what an "agent" actually is.

| Concept | What to understand |
|---|---|
| **Agent loop** | The cycle of: observe → think/plan → act (tool call) → observe result → repeat until done or stopped |
| **Tools vs. Agents** | A *tool* is a bounded, deterministic capability (e.g., a bash executor, a search API). An *agent* decides which tools to call, in what order, and when to stop |
| **Context window mechanics** | Tokens, context limits, why models "forget" things, why huge tool outputs cause problems |
| **Model capability vs. harness capability** | The model is increasingly a commodity; what differentiates agent *products* is the harness around it |

**Exercises:**
- Build the simplest possible agent loop by hand (no framework): a while-loop that calls an LLM API, parses a tool call, executes it, feeds the result back. Do this in raw code before touching any agent framework — you need to feel the loop.
- Deliberately break it: feed it a huge tool output and watch context degrade ("context rot"). Note what goes wrong.

**Ready to move on when:** you can explain, without a framework's help, exactly what happens between a user's message and the agent's final answer.

---

## Phase 1 — Understand the Anatomy of a Harness

This is the conceptual core. A harness is **everything except the model**.

### 1.1 The two ingredients of a harness: Guides and Sensors

| Type | Purpose | Examples |
|---|---|---|
| **Guides** (feedforward) | Steer behavior *before* it happens | System prompts, `AGENTS.md` / instruction files, constraint documents, few-shot examples, style guides |
| **Sensors** (feedback) | Verify behavior *after* it happens | Evals, automated tests, linters, output parsers/validators, screenshot diffing, human review gates |

**Key insight:** Guides alone → agent repeats the same class of mistake forever (nothing checks it). Sensors alone → agent has no way to avoid known failure modes upfront (everything is trial and error). **You need both, working as a loop.**

### 1.2 The five (or so) pillars of a harness

Study each of these as a distinct subsystem you will need to design:

1. **Tool orchestration** — what tools exist, what permissions they carry, how they're invoked, sandboxing
2. **Context management** — what the model sees at each turn: retrieval, compaction, summarization, pruning
3. **Memory & state** — task state, project memory, progress artifacts that persist across turns/sessions
4. **Verification & failure attribution** — how you know something worked; how you diagnose *why* it failed before retrying
5. **Observability & permissions** — logging/tracing of every decision; guardrails and human-in-the-loop checkpoints

### 1.3 Harness vs. adjacent disciplines (don't confuse these)

| Term | Scope |
|---|---|
| **Prompt engineering** | Crafting a single prompt/instruction |
| **Context engineering** | Managing *what the model sees* — a subset of harness engineering |
| **Harness engineering** | The full system: context + tools + memory + verification + observability + permissions |

**Exercises:**
- Take an agent failure you've personally experienced (yours or a known public one) and classify it: was it a *guide* gap (agent didn't know the rule) or a *sensor* gap (nothing caught it)?
- Sketch (on paper) the 5 pillars for a hypothetical "customer support agent" — just labels, no implementation yet.

**Ready to move on when:** given any agent failure story, you can immediately say which pillar broke.

---

## Phase 2 — Study Real Harness Designs

Don't design in a vacuum — study how production systems are actually built.

### 2.1 Read/dissect these reference architectures
- **Coding agent harnesses** (Claude Code, Cursor, GitHub Copilot, Codex, Gemini CLI): how do they structure `AGENTS.md`/`CLAUDE.md`-style instruction files? What's their tool set (file system, shell, test runner, git, browser)?
- **The "initializer–executor" pattern**: a planning step that sets up structure, followed by an execution step that makes incremental, verifiable progress
- **Multi-context-window continuity**: how agents track progress across sessions when the context window resets (progress files, checklists, state snapshots)

### 2.2 Compare configuration mechanisms across tools
Look specifically at how different products expose harness controls to users:
- Static instruction files (`AGENTS.md`, `CLAUDE.md`)
- "Skills" (packaged, reusable instruction+script bundles)
- Sub-agents (delegating a sub-task to a separately-scoped agent)
- Hooks/callbacks at specific lifecycle points (pre-tool-call, post-tool-call, session-start)

### 2.3 Builder harness vs. user harness
- **Builder harness** — baked in by the product creator (system prompt, orchestration, default tools). You mostly can't change this.
- **User/team harness** — what *you* build on top for your specific use case: your own instruction files, your own verification scripts, your own sub-agents.
- As a developer, your job is almost always **user harness engineering**, even if you're curious about the builder layer too.

**Exercises:**
- Pick two agentic coding tools you have access to. Open and compare their instruction-file conventions side by side. Note what's shared (interoperability standards) vs. proprietary.
- Find a public `AGENTS.md` file (many open-source repos have one) and annotate each line: what specific failure do you think it was written to prevent?

**Ready to move on when:** you can point to 3+ concrete design patterns from real tools and explain the failure mode each one addresses.

---

## Phase 3 — Design Your Own Harness (Core Skill)

This is where you move from studying to designing. Work through each pillar for **your specific agent**.

### 3.1 Guides — write your instruction file
- Draft an `AGENTS.md`-equivalent for your agent. Start minimal.
- **Critical practice:** every line should map to either (a) a task requirement or (b) a specific observed failure. Don't pre-guess rules — earn them from real failures.
- Include: scope/goals, constraints ("never do X"), tool-use conventions, output format expectations, escalation rules (when to ask a human).

### 3.2 Tools — design the tool surface
- List every action your agent needs to take in the real world.
- For each tool: define inputs/outputs, failure modes, and **permission level** (read-only vs. read-write vs. destructive).
- Principle: make the *safe* path the *easy* path. If an agent keeps making a mistake, ask "can I build a tool that makes this mistake physically impossible?" rather than "can I prompt harder?"

### 3.3 Context management
- Decide what goes into context at each step: full history? Summarized history? Retrieved-on-demand (RAG-style) documents?
- Design **compaction**: when/how older turns get summarized into condensed notes instead of kept verbatim.
- Design **retrieval**: pull only what's relevant to the current step rather than loading everything upfront.

### 3.4 Memory & state
- What needs to survive across a single session? Across multiple sessions/days?
- Common artifacts: a running task list/checklist file, a "decisions log," a project-memory doc updated as the agent learns things about the environment.

### 3.5 Verification & failure attribution ("sensors")
- For every task type, define: **what does "done correctly" mean, mechanically?** (a passing test, a schema match, a rendered screenshot that matches expectations, a human sign-off)
- Build lightweight, cheap-to-run checks that fire on every agent action, not just at the end.
- Design a **failure-attribution step**: before letting the agent retry, force a classification of *why* it failed (wrong tool? bad assumption? missing context? environment issue?). Don't let it just "try again blindly."

### 3.6 Observability & permissions
- Log: what tools were called, with what arguments, what the agent's stated reasoning was, what the outcome was.
- Define permission boundaries and where a human must approve before the agent proceeds (e.g., before a destructive action, a production deploy, an external message send).

### 3.7 Apply the 5 design principles as a checklist
Use this checklist on your own design:
- [ ] **P1 — Explicit resources:** Are context budget, tool affordances, memory, and permissions all *named and visible*, not implicit?
- [ ] **P2 — Traceable mediation:** Can you reconstruct, after the fact, exactly how the agent chose its context/tools/recovery path?
- [ ] **P3 — Requirement-level verification:** Is "done" bound to a deterministic check, not just an LLM saying "done"?
- [ ] **P4 — Attribution before recovery:** Does a failure get diagnosed before the agent is allowed to retry?
- [ ] **P5 — Maintenance/entropy awareness:** Does your instruction file/harness have a plan for staying current as the project changes (not just growing forever)?

**Exercises:**
- Full design doc for your actual agent: one page per pillar (3.1–3.6), plus the checklist filled in.
- Peer review: have someone else try to break your design on paper before you write code — cheaper to fix now.

**Ready to move on when:** you have a written design doc covering all 6 sub-areas, reviewed at least once.

---

## Phase 4 — Implement, Instrument, Iterate

### 4.1 Build incrementally, in this order
1. Minimal agent loop + smallest possible tool set
2. Add your instruction file (guides)
3. Add one sensor/verification check at a time — resist building all of them upfront
4. Add context management (compaction/retrieval) only once you actually hit context limits
5. Add memory/state persistence once you need multi-session continuity
6. Add observability/logging from day one of real usage — don't bolt it on later

### 4.2 The core feedback loop — "treat every failure as an engineering problem"
When the agent makes a mistake:
1. **Attribute** — classify why, precisely (don't just re-prompt and hope)
2. **Fix the environment, not just the prompt** — can you build a tool, a constraint, or a check that makes this class of mistake structurally impossible next time?
3. **Encode it** — add one line to your instruction file *or* one new sensor, tied to this specific observed failure
4. **Verify the fix** — re-run the scenario that caused the failure to confirm it's actually closed

This loop, run repeatedly, is the actual day-to-day practice of harness engineering.

### 4.3 Watch for common failure patterns and their fixes
| Symptom | Likely pillar | Typical fix |
|---|---|---|
| Agent repeats the same mistake across sessions | Guides | Add a rule to instruction file |
| Agent "says" it's done but output is wrong | Verification | Add a deterministic check (test/schema/lint) instead of trusting self-report |
| Agent loses track of earlier decisions | Memory/state | Add a persistent progress/decision log file |
| Agent's responses degrade in long sessions | Context management | Add compaction/summarization; trim tool-output size |
| Agent takes destructive action without oversight | Permissions | Add human-approval gate for that action class |
| You can't tell why the agent did something | Observability | Add structured logging of tool calls + reasoning |

**Exercises:**
- Run your agent on 10–20 real tasks. Log every failure. For each, apply the 4-step loop above.
- After a week of real use, revisit your Phase 3 design doc and update it — note what you got wrong initially.

**Ready to move on when:** your agent's failure rate on a fixed task set is measurably dropping over successive iterations, and you have a paper trail (logs) showing why.

---

## Phase 5 — Scale, Maintain, and Go Deeper

### 5.1 Scaling patterns
- **Sub-agents**: delegate bounded sub-tasks to separately-scoped agents with their own harness (own tools, own instructions) — useful when a single agent's context/tool surface gets too broad
- **Multi-agent orchestration / agent-to-agent communication**: if your system needs multiple agents to coordinate, study current interoperability standards for agent discovery and delegation
- **Skills packaging**: bundle reusable instruction+script combos so common capabilities aren't re-derived from scratch each time

### 5.2 Maintenance — the part most people skip
- Instruction files should not just grow forever — periodically prune/consolidate rules that are now redundant (because a tool/check now enforces them structurally)
- Track "maintenance burden": how much human attention does this harness need per week to keep working? If it's rising, something needs to move from a guide (prompt rule) to a sensor (automated check) or a tool constraint (structural fix)
- Re-evaluate your tool/permission surface as the agent's scope grows — permission creep is a real risk

### 5.3 Advanced topics to explore once the basics are solid
- Cybernetics-inspired control theory applied to agent design (feedback vs. feedforward formalisms)
- Evals-as-harness: building systematic, repeatable evaluation suites rather than ad hoc spot checks
- Cost/token budget management as an explicit harness resource, not an afterthought
- Security-specific harness concerns: prompt injection defenses, tool-permission sandboxing, data exfiltration prevention

**Exercises:**
- Do a "harness audit" of your system: for every rule in your instruction file, can you say whether it's still needed, or whether it's now redundant with a tool/sensor?
- Write a one-page "harness spec" for your agent as if handing it off to another engineer — this is often the clearest sign of whether your design is actually legible.

---

## Quick-Reference: The Full Checklist

Use this as a living checklist while building any agent:

- [ ] Agent loop understood and built by hand at least once
- [ ] Instruction file (guides) exists and maps to real, observed needs — not speculative rules
- [ ] Tool surface is explicitly scoped with permission levels per tool
- [ ] Context management strategy defined (what's kept, compacted, retrieved)
- [ ] Memory/state persistence designed for cross-session continuity
- [ ] Verification is deterministic where possible (tests/schemas/lint), not just self-report
- [ ] Failures are attributed (classified) before retry is allowed
- [ ] Every tool call and key decision is logged/traceable
- [ ] Human-approval gates exist for destructive/high-stakes actions
- [ ] There's a repeatable loop: fail → attribute → fix environment → encode → re-verify
- [ ] Instruction file/harness has a maintenance plan (pruning, not just growing)

---

## Suggested Learning Sequence (Summary Timeline)

| Week(s) | Focus |
|---|---|
| 1 | Phase 0 — Build a raw agent loop by hand |
| 1–2 | Phase 1 — Learn the guides/sensors model and the 5 pillars |
| 2–3 | Phase 2 — Study 2–3 real harness implementations |
| 3–5 | Phase 3 — Design your own harness on paper, pillar by pillar |
| 5–8 | Phase 4 — Implement incrementally, run real tasks, iterate the fail→fix loop |
| 8+ | Phase 5 — Scale (sub-agents, skills), maintain, go deeper into advanced topics |

---

*Note: "Harness engineering" is a fast-evolving term (most current usage dates to early/mid-2026), so specific tool conventions (e.g., instruction file formats) are likely to keep shifting — treat the underlying principles above (guides + sensors, the 5 pillars, the fail→attribute→fix loop) as the stable core, and the tool-specific details as things to re-check periodically.*
