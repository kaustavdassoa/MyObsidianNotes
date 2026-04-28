### From Zero to Advanced Team Automation — Senior Professional  Edition
---

## Table of Contents

1. [Prerequisites & Setup](#1-prerequisites--setup)
2. [Phase 1 — Core Concepts](#2-phase-1--core-concepts-12-days)
3. [Phase 2 — Workflow Automation](#3-phase-2--workflow-automation-35-days)
4. [Phase 3 — Multi-Agent Orchestration](#4-phase-3--multi-agent-orchestration-12-weeks)
5. [Phase 4 — Production & Cost Mastery](#5-phase-4--production--cost-mastery-ongoing)
6. [API Billing Strategy](#6-api-billing-strategy)
7. [CLAUDE.md Mastery](#7-claudemd-mastery)
8. [MCP Server Integrations](#8-mcp-server-integrations)
9. [Team Deployment Playbook](#9-team-deployment-playbook)
10. [Key Resources](#10-key-resources)
11. [Quick Reference Cheatsheet](#11-quick-reference-cheatsheet)

---

## 1. Prerequisites & Setup

### System Requirements

| Requirement | Minimum | Recommended |
|---|---|---|
| Node.js | v18+ | v20 LTS |
| OS | macOS / Linux / Windows WSL2 | macOS or Ubuntu |
| RAM | 4 GB | 16 GB+ |
| API Key | Anthropic Console account | Workspace with spend limits |

### Installation (API billing path)

```bash
# 1. Install Claude Code globally
npm install -g @anthropic-ai/claude-code

# 2. Set your API key (add to ~/.zshrc or ~/.bashrc)
export ANTHROPIC_API_KEY="sk-ant-..."

# 3. Verify installation
claude --version

# 4. Install ccusage for cost monitoring (critical for API billing)
npm install -g ccusage

# 5. Optional: VS Code extension
# Search "Claude Code" in Extensions (Cmd+Shift+X)
```

### Free Credits to Get Started

- **$5 API credits** — Create account at `platform.anthropic.com`, no credit card needed. Claim promptly — expires 14 days after clicking "Claim" in the Console dashboard.
- **Guest Pass (7 days)** — Ask a Max subscriber for a pass via `/passes` command. Find them on r/ClaudeAI or AI Discord communities.
- **Open Source program** — If you maintain a project with 5,000+ GitHub stars or 1M+ monthly npm downloads, apply for 6 months of Max 20x free.

---

## 2. Phase 1 — Core Concepts (1–2 days)

### 2.1 Understand What Claude Code Actually Is

Claude Code is a **terminal-native agentic coding tool** — not a chatbot wrapper. It:

- Reads your entire codebase and maintains project context
- Writes, edits, and runs code with your explicit approval
- Operates through a permission system (you approve every action)
- Remembers project-specific context across sessions via memory files
- Connects to external services via MCP (Model Context Protocol) servers

### 2.2 First Session Checklist

```bash
# Navigate to your project
cd ~/your-project

# Start Claude Code
claude

# Inside the session — try these in order:
> /help                          # See all available commands
> explain the structure of this codebase
> /plan                          # Switch to Plan Mode
> refactor the auth module to use JWT instead of sessions
> /cost                          # Check token usage for this session
> /exit
```

### 2.3 The Three Access Modes

| Mode | How to Launch | Best For |
|---|---|---|
| **CLI (Terminal)** | `claude` | Primary daily driver, full agent capabilities |
| **VS Code Extension** | Open Command Palette → "Claude Code" | Inline diffs, plan review in editor |
| **Desktop App** | Download from claude.ai | Visual diff review, multiple sessions side by side |

### 2.4 Plan Mode — Your Most Important Habit

Always use Plan Mode before expensive multi-file operations.

```bash
# Trigger Plan Mode
> /plan

# Then describe the task
> Refactor the authentication module to use JWT tokens, update all 
  12 endpoints that use session-based auth, and update the test suite

# Claude outlines the approach and waits for approval before executing
# Review → ask questions → approve
```

**Use Plan Mode for:**
- Renaming a module imported across 20+ files
- Adding a new feature touching routes, controllers, models, and tests
- Any refactor in a codebase you don't fully understand yet
- Security-sensitive changes

**Skip Plan Mode for:**
- Single-file edits
- Generating boilerplate (components, tests for a single function)
- Explaining or summarizing code

### 2.5 The Memory System

Claude Code has three memory layers:

| Layer | Where It Lives | Scope | Auto or Manual |
|---|---|---|---|
| **CLAUDE.md** | Project root or `~/.claude/CLAUDE.md` | Project or global | Manual |
| **Auto-memory** | `~/.claude/` JSONL files | Session learnings | Automatic |
| **MCP context** | External services | Dynamic, real-time | Tool-driven |

### 2.6 Phase 1 Resources

- Official docs: `code.claude.com/docs/en/overview`
- Official free course: `anthropic.skilljar.com/claude-code-in-action`
- Beginner guide: `codewithmukesh.com/blog/claude-code-for-beginners`

---

## 3. Phase 2 — Workflow Automation (3–5 days)

### 3.1 CLAUDE.md — Your Highest-Leverage File

`CLAUDE.md` is read automatically at the start of every session. Think of it as the onboarding doc you'd give a new senior engineer joining your team.

**Critical rule: Keep it under 200 lines.** Every token in this file is a tax on every single API request.

**Template for a team repo:**

```markdown
# Project: [Your Project Name]

## Tech stack
- Runtime: Node.js 20 + TypeScript 5.3
- Framework: Fastify + Prisma + PostgreSQL
- Testing: Vitest + Supertest
- CI: GitHub Actions

## Architecture decisions
- All new endpoints follow RESTful conventions in src/routes/
- Business logic lives in src/services/ — never in route handlers
- Database queries only in src/repositories/
- All async errors use Result<T, E> pattern (no thrown exceptions in services)

## Coding standards
- Use named exports, never default exports
- Prefer `const` over `let`; never `var`
- All public functions require JSDoc with @param and @returns
- Run `npm run lint` before committing

## Common commands
- `npm run dev` — start dev server with hot reload
- `npm test` — run test suite
- `npm run db:migrate` — apply pending migrations
- `npm run build` — type-check + compile

## What NOT to do
- Do not modify src/core/auth.ts without explicit approval
- Do not add new npm dependencies without listing them here first
- Do not skip writing tests for new service methods
```

### 3.2 Hooks — Enforce Team Standards Automatically

Hooks run shell commands before or after Claude Code actions. This is how you make Claude a team-aware collaborator rather than a lone-wolf agent.

**Hook configuration lives in `.claude/hooks.json`:**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|Create",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint --silent"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Running shell command — review output carefully'"
          }
        ]
      }
    ]
  }
}
```

**Useful hook patterns:**

| Hook Trigger | Command | Purpose |
|---|---|---|
| After file edit | `npm run lint` | Auto-format and catch style violations |
| Before commit | `npm test -- --run` | Prevent broken commits |
| After file create | `npx tsc --noEmit` | Catch TypeScript errors immediately |
| Before bash | `echo "CMD: $CLAUDE_TOOL_INPUT"` | Audit trail of all shell commands |

### 3.3 Custom Slash Commands

Slash commands package repeatable workflows your whole team can run. They live in `.claude/commands/` and are committed to your repo.

**Creating a PR review command** — `.claude/commands/review-pr.md`:

```markdown
Review the current git diff and staged changes as if you are a senior engineer doing a code review.

Check for:
1. Logic errors and edge cases
2. Security vulnerabilities (SQL injection, XSS, unvalidated input)
3. Missing error handling
4. Performance concerns (N+1 queries, missing indexes, unnecessary loops)
5. Test coverage gaps
6. Violations of our coding standards in CLAUDE.md

Format your review as:
- **Critical** (must fix before merge)
- **Important** (should fix, can merge with follow-up ticket)
- **Suggestion** (optional improvements)
- **Praise** (what was done well)
```

**More command ideas:**

```bash
.claude/commands/
├── review-pr.md          # /review-pr
├── write-tests.md        # /write-tests
├── explain-module.md     # /explain-module
├── fix-security.md       # /fix-security
├── generate-docs.md      # /generate-docs
├── deploy-staging.md     # /deploy-staging
└── debug-prod.md         # /debug-prod
```

### 3.4 .claudeignore — Reduce Token Waste

Create `.claudeignore` in your project root to prevent Claude from reading irrelevant files:

```
node_modules/
dist/
build/
.next/
coverage/
*.log
*.lock
.env*
__pycache__/
.DS_Store
*.min.js
*.min.css
```

This can reduce token consumption by 30–60% on large repos.

### 3.5 Phase 2 Resources

- `github.com/luongnv89/claude-howto` — Visual, example-driven guide with copy-paste templates
- `github.com/FlorianBruniaux/claude-code-ultimate-guide` — Production-ready templates for hooks, commands, and MCP configs

---

## 4. Phase 3 — Multi-Agent Orchestration (1–2 weeks)

This is where Claude Code scales from personal productivity to team infrastructure.

### 4.1 Sub-Agents — Context-Isolated Specialists

Sub-agents are Claude instances spawned with focused, isolated context to handle specific subtasks. Each sub-agent has its own context window, preventing context bleed on large tasks.

**The pattern that works:**

```
Lead agent
├── Analyzes the overall task
├── Decomposes into isolated subtasks
├── Spawns sub-agent A → handles API layer changes
├── Spawns sub-agent B → updates test suite
├── Spawns sub-agent C → updates documentation
└── Synthesizes and reviews all results
```

**Prompt pattern for orchestrating sub-agents:**

```
You are a lead engineer orchestrating a large refactor.

Task: Migrate our REST API from Express to Fastify.

Spawn separate sub-agents for each of these isolated workstreams:
1. Route migration (src/routes/) — convert Express syntax to Fastify plugins
2. Middleware migration (src/middleware/) — convert Express middleware to Fastify hooks
3. Test updates (src/tests/) — update all test helpers and mocks
4. Documentation (docs/) — update API docs and README

Each sub-agent should work independently. After all complete, 
review the combined result for integration issues.
```

### 4.2 Agent Teams — Parallel Execution

Agent Teams spin up multiple Claude Code agents simultaneously, coordinated by a lead agent. Best for:

- Large-scale refactors across multiple modules
- Parallel feature development with a shared context
- Multi-service migrations (each service gets its own agent)
- Comprehensive test suite generation

**Enabling Agent Teams:**

```bash
# In your Claude Code session
> /agents

# Or specify in your prompt
> Using parallel agents, analyze the entire src/ directory for 
  security vulnerabilities. Have one agent per major module:
  auth, payments, user-management, and api-gateway.
```

**Real-world impact benchmarks:**

| Company | Use Case | Result |
|---|---|---|
| Fountain | Multi-file refactors | 50% faster delivery |
| CRED | Feature development | 2x speed increase |
| TELUS | Codebase analysis | 500,000 hours saved |
| Rakuten | Autonomous debugging | 7 hours autonomous operation |

### 4.3 Agent SDK — Build Your Own Orchestration

For fully custom workflows, the Agent SDK gives you programmatic control over Claude Code's capabilities:

```typescript
import { ClaudeCode } from '@anthropic-ai/claude-code/sdk';

const agent = new ClaudeCode({
  model: 'claude-sonnet-4-6',
  tools: ['read_file', 'write_file', 'run_command'],
  permissions: {
    allowShellCommands: false,   // Restrict to read/write only
    allowNetworkAccess: false,
  }
});

// Run an automated code review pipeline
const result = await agent.run(`
  Review all TypeScript files in src/services/ for:
  1. Missing error handling
  2. Functions over 50 lines
  3. Missing JSDoc comments
  
  Output a JSON report with file, line, severity, and description.
`);

console.log(JSON.parse(result.output));
```

**SDK use cases for team automation:**

- Nightly code quality reports sent to Slack
- Automated PR review bots in GitHub Actions
- On-demand documentation regeneration
- Security scan automation before deployments

### 4.4 Git Worktrees for Parallel Agents

Git worktrees let multiple agents work on different branches simultaneously without conflicts:

```bash
# Set up parallel workspaces
git worktree add ../project-feature-auth feature/jwt-auth
git worktree add ../project-feature-payments feature/stripe-v2
git worktree add ../project-refactor-db refactor/prisma-migration

# Run a Claude Code session in each simultaneously
cd ../project-feature-auth && claude
cd ../project-feature-payments && claude
cd ../project-refactor-db && claude
```

### 4.5 Phase 3 Resources

- `github.com/FlorianBruniaux/claude-code-ultimate-guide` — Sections 9.17–9.20 cover Agent Teams with production metrics
- Boris Cherny's workflow (Head of Claude Code at Anthropic): parallel agents + shared CLAUDE.md + Plan Mode + verification hooks

---

## 5. Phase 4 — Production & Cost Mastery (Ongoing)

### 5.1 CI/CD Integration

**GitHub Actions — Automated PR Review:**

```yaml
# .github/workflows/claude-review.yml
name: Claude Code PR Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run automated review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          git diff origin/main...HEAD > /tmp/pr-diff.txt
          claude --no-interactive \
            "Review this PR diff for bugs, security issues, and style violations.
             Format as GitHub-flavored markdown with severity labels.
             $(cat /tmp/pr-diff.txt)" \
            > review.md
          cat review.md

      - name: Post review as comment
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const review = fs.readFileSync('review.md', 'utf8');
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## Claude Code Review\n\n${review}`
            });
```

**Other CI/CD use cases:**

```bash
# Nightly doc generation
claude --no-interactive "Generate API documentation for all public methods in src/api/"

# Security scan before deploy
claude --no-interactive "Scan src/ for OWASP Top 10 vulnerabilities. Exit with code 1 if critical issues found."

# Test coverage gap analysis
claude --no-interactive "Identify all service methods in src/services/ that lack unit tests"
```

### 5.2 Checkpoints & Long-Running Sessions

For sessions that run 30+ minutes:

```bash
# Create a checkpoint before a risky operation
> /checkpoint save "before-auth-refactor"

# If something goes wrong, restore
> /checkpoint restore "before-auth-refactor"

# List all checkpoints
> /checkpoint list
```

### 5.3 Team Onboarding Template

Standard structure to commit to every repo using Claude Code:

```
your-project/
├── CLAUDE.md                    # Project-level Claude instructions
├── .claudeignore                # Files Claude should never read
├── .claude/
│   ├── hooks.json               # Automation hooks
│   └── commands/
│       ├── review-pr.md         # /review-pr
│       ├── write-tests.md       # /write-tests
│       ├── fix-security.md      # /fix-security
│       ├── generate-docs.md     # /generate-docs
│       └── deploy-staging.md    # /deploy-staging
└── .github/
    └── workflows/
        └── claude-review.yml    # Automated PR review
```

---

## 6. API Billing Strategy

Since you're on pay-per-token, cost management is an operational discipline, not an afterthought.

### 6.1 Current API Pricing (April 2026)

| Model | Input (per MTok) | Output (per MTok) | Best For |
|---|---|---|---|
| Claude Opus 4.7 | $5.00 | $25.00 | Complex reasoning, architecture decisions |
| Claude Sonnet 4.6 | $3.00 | $15.00 | **Default daily driver — best value** |
| Claude Haiku 4.5 | $1.00 | $5.00 | Classification, summarization, simple tasks |

**Extended context pricing:** Sonnet 4.6 uses higher rates ($6.00/$22.50 per MTok) for prompts over 200K tokens.

### 6.2 The Three Cost Levers (Ranked by Impact)

**1. Prompt Caching (up to 90% savings on input)**

```
Standard input cost:   $3.00 per MTok
Cache write cost:      $3.75 per MTok (1.25x, 5-min TTL)
Cache read cost:       $0.30 per MTok (0.1x) ← 90% savings

What gets cached automatically:
- CLAUDE.md content
- System prompts
- File context that doesn't change between turns
```

**2. Batch API (50% off for non-real-time work)**

```bash
# Standard Sonnet 4.6: $3.00/$15.00 per MTok
# Batch API Sonnet 4.6: $1.50/$7.50 per MTok

# Use Batch API for:
# - Nightly code analysis jobs
# - Bulk documentation generation  
# - Offline security scans
# - Large-scale refactor planning
```

**3. Model Selection by Task**

```
Task type               → Model        → Reason
─────────────────────────────────────────────────
Complex architecture    → Opus 4.7     → Worth the cost, fewer correction passes
Daily feature work      → Sonnet 4.6  → Best capability/cost balance
CI/CD automated checks  → Haiku 4.5   → Fast, cheap, good enough for structured tasks
Test generation         → Sonnet 4.6  → Needs codebase understanding
Simple Q&A / lookup     → Haiku 4.5   → Overkill to use Sonnet
```

### 6.3 Cost Monitoring Setup

```bash
# Install ccusage (reads from ~/.claude/ JSONL logs)
npm install -g ccusage

# Daily cost report
ccusage daily

# Monthly breakdown by model
ccusage monthly --by-model

# Set a daily spend alert (add to your shell profile)
alias check-ai-cost="ccusage daily && echo 'Budget: $10/day'"
```

**Console-level controls (for team accounts):**

1. Go to `console.anthropic.com`
2. Set workspace spend limit (e.g., $500/month hard cap)
3. Create per-team API keys with individual rate limits
4. Enable billing alerts at 50% and 80% of budget

### 6.4 Budget Benchmarks

| Usage Pattern | Expected Cost |
|---|---|
| Light (learning, occasional use) | $3–8/day |
| **Average developer (recommended budget)** | **$6–12/day** |
| Heavy (full-time, multiple agents) | $20–50/day |
| Max plan breakeven vs API | ~50 sessions/month |

**Rule of thumb:** API billing beats Pro subscription if you use Claude Code fewer than ~50 sessions per month. Heavy daily users should consider Max ($100–$200/month) where subscription economics win.

---

## 7. CLAUDE.md Mastery

### 7.1 The Three-Level CLAUDE.md Hierarchy

```
~/.claude/CLAUDE.md          # Global — applies to all projects
  └── project/CLAUDE.md      # Project — overrides global
        └── src/CLAUDE.md    # Directory — overrides project (advanced)
```

### 7.2 Global CLAUDE.md Template

Create `~/.claude/CLAUDE.md` for preferences that apply everywhere:

```markdown
# Global Claude preferences

## My defaults
- I am a senior engineer. Skip beginner explanations.
- Default language: TypeScript unless the project uses something else
- Always use async/await over Promise chains
- Prefer functional patterns (map/filter/reduce) over imperative loops

## Output format
- For code reviews: group by severity (Critical, Important, Suggestion)
- For explanations: lead with the "why", then the "what", then the "how"
- For refactors: show a before/after diff, not just the final result

## What I don't want
- Do not add comments explaining what every line does — only explain why
- Do not suggest adding console.log for debugging
- Do not wrap every function in a try/catch — follow the project's error pattern
```

### 7.3 Advanced CLAUDE.md Patterns

**Progressive disclosure** — put the most critical constraints first:

```markdown
# CRITICAL (read first)
- NEVER modify src/core/payments/ without explicit approval
- ALWAYS run `npm test` after any change to src/services/
- Database migrations must be reviewed by a human before running

# IMPORTANT
[standards and conventions]

# REFERENCE
[architecture overview, command list]
```

---

## 8. MCP Server Integrations

MCP (Model Context Protocol) servers let Claude Code interact with external systems.

### 8.1 Commonly Used MCP Servers for Team Automation

| Server | Use Case | Config |
|---|---|---|
| `@modelcontextprotocol/server-github` | PR review, issue management | Requires GitHub token |
| `@modelcontextprotocol/server-slack` | Send reports to channels | Requires Slack bot token |
| `@modelcontextprotocol/server-postgres` | Query databases safely | Read-only recommended |
| `@modelcontextprotocol/server-filesystem` | Extended file operations | Configure allowed paths |
| `@modelcontextprotocol/server-brave-search` | Web search in sessions | Requires Brave API key |

### 8.2 MCP Configuration

Add to `.claude/mcp-config.json`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "${DATABASE_URL}"
      }
    }
  }
}
```

### 8.3 Power Workflow: GitHub MCP + Sub-Agents

```
/review-pr trigger
    ↓
Lead agent fetches PR via GitHub MCP
    ↓
Spawns 3 sub-agents in parallel:
  ├── code-reviewer → logic, errors, edge cases
  ├── security-scanner → OWASP Top 10 checks
  └── test-engineer → coverage gaps, missing test cases
    ↓
Lead agent synthesizes findings
    ↓
Posts structured review comment via GitHub MCP
```

---

## 9. Team Deployment Playbook

### 9.1 Rolling Out Claude Code to a Team

**Week 1 — Pilot with 2–3 engineers**
- Set up shared `CLAUDE.md` in repo
- Define 3 initial slash commands (`/review-pr`, `/write-tests`, `/explain-module`)
- Set up `ccusage` for everyone
- Establish a `#claude-code` Slack channel for sharing wins and patterns

**Week 2 — Hooks and CI/CD**
- Add `hooks.json` for lint and test automation
- Set up GitHub Actions PR review bot
- Add `.claudeignore` to all repos

**Week 3 — Agents and scale**
- Train team on Plan Mode discipline
- Introduce sub-agent patterns for large tasks
- Set up BYOK with per-team API keys and spend limits

**Week 4 — Optimization**
- Audit `CLAUDE.md` files — cut anything over 200 lines
- Review `ccusage monthly` — identify expensive patterns
- Collect team feedback → update slash commands

### 9.2 BYOK Team Setup

```
1. Create an Anthropic Console workspace at console.anthropic.com
2. Set a workspace monthly spend limit (start with $500 for a 5-person team)
3. Create per-team API keys (one per team or one per engineer)
4. Set individual key rate limits
5. Enable billing alerts at 50%, 80%, 100% of budget
6. Distribute keys via your secrets manager (not Slack or email)
7. Pin Claude Code version in CI: npm install -g @anthropic-ai/claude-code@2.1.34
```

### 9.3 Cost Governance Rules

```markdown
# Team cost rules (add to CLAUDE.md)

## Model selection policy
- Default: claude-sonnet-4-6
- Opus only: architectural decisions, complex security analysis
- Haiku only: CI/CD automated checks, simple classification tasks

## Session hygiene
- Run /cost before ending any session over 30 minutes
- Use /plan before any task touching 5+ files
- Keep CLAUDE.md under 200 lines (audit monthly)
- Add to .claudeignore: node_modules, dist, build, coverage, *.log

## Weekly review
- Team lead reviews ccusage monthly report every Monday
- Any day over $20/developer triggers a pattern review
```

---

## 10. Key Resources

### Official Resources

| Resource | URL | What It Covers |
|---|---|---|
| Official docs | `code.claude.com/docs` | Feature reference, API, configuration |
| Official free course | `anthropic.skilljar.com/claude-code-in-action` | Architecture, MCP, GitHub integration |
| Anthropic Console | `console.anthropic.com` | API keys, billing, usage monitoring |
| Claude pricing | `claude.com/pricing` | Current subscription and API rates |

### Community Guides (Ranked by Depth)

| Resource | URL | Best For |
|---|---|---|
| claude-code-ultimate-guide | `github.com/FlorianBruniaux/claude-code-ultimate-guide` | Most comprehensive, beginner to power user |
| claude-howto | `github.com/luongnv89/claude-howto` | Visual tutorials, copy-paste templates, 10 modules |
| Claude Code Handbook | `freecodecamp.org/news/claude-code-handbook` | Professional introduction, workflow philosophy |
| codewithmukesh series | `codewithmukesh.com/blog/claude-code-for-beginners` | .NET-focused but widely applicable |

### YouTube Courses

| Creator | Length | Focus |
|---|---|---|
| Nick Saraev | 4 hours | Technical deep-dive — Agent Teams, Git worktrees, MCP, cloud deployment |
| Sabrina Ramonov | 2 hours | Practical automation — hooks, sub-agents, content pipelines |

**Recommended order:** Nick's course for technical depth → Sabrina's for workflow automation patterns.

---

## 11. Quick Reference Cheatsheet

### Essential Commands

```bash
# Session management
claude                    # Start a session in current directory
claude --no-interactive   # Headless mode for CI/CD
/plan                     # Switch to Plan Mode (approve before execute)
/cost                     # Show token usage for current session
/checkpoint save "name"   # Save a restore point
/exit                     # End session

# Context and memory
/memory                   # View current memory and CLAUDE.md
/context                  # Debug context window usage
/clear                    # Clear conversation history

# Agents
/agents                   # Enable Agent Teams mode
/passes                   # Generate Guest Passes (Max subscribers only)

# Custom commands (your slash commands)
/review-pr                # Run .claude/commands/review-pr.md
/write-tests              # Run .claude/commands/write-tests.md
```

### Model Selection Quick Reference

```bash
# Set model for session
claude --model claude-sonnet-4-6     # Default recommendation
claude --model claude-opus-4-7       # Complex architecture tasks
claude --model claude-haiku-4-5      # Fast, cheap, CI/CD tasks
```

### ccusage Commands

```bash
ccusage daily                        # Today's cost
ccusage monthly                      # This month's cost
ccusage monthly --by-model           # Cost breakdown by model
ccusage session                      # Current session cost
```

### Cost Estimation Quick Math

```
Sonnet 4.6 typical session (medium codebase):
  ~100K input tokens  × $3.00/MTok  = $0.30
  ~20K output tokens  × $15.00/MTok = $0.30
  Total per session: ~$0.60

With prompt caching (context reused):
  ~10K fresh input    × $3.00/MTok  = $0.03
  ~90K cached reads   × $0.30/MTok  = $0.027
  ~20K output         × $15.00/MTok = $0.30
  Total per session: ~$0.36 (40% cheaper)

Rule of thumb:
  $6/day = healthy, learning/moderate use
  $12/day = active daily use
  $20+/day = heavy multi-agent workflows
```

### The Non-Negotiable Habits

1. **Always run `/plan` before touching 5+ files.** Planning prevents costly rework.
2. **Keep `CLAUDE.md` under 200 lines.** Every token in it multiplies across every request.
3. **Add `.claudeignore` to every repo.** Skip `node_modules/`, `dist/`, `*.log`.
4. **Check `/cost` at the end of long sessions.** Awareness prevents bill shock.
5. **Use Sonnet 4.6 by default, Opus only when justified.** The cost difference is 5x.
6. **Pin your Claude Code version in CI.** Silent upgrades have caused 40%+ token inflation bugs.
7. **Use `ccusage monthly` on the first of each month.** Cost visibility drives cost discipline.

---

*Last updated: April 2026 — Claude Code v2.1.x, Sonnet 4.6, Opus 4.7*  
*Sources: Anthropic official docs, code.claude.com, console.anthropic.com*
