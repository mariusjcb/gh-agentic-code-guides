# 🗺️ Course Map — Your Learning Journey

> Follow the path from left to right. Each step builds on the previous one.

---

## 📍 The Journey

```mermaid
graph LR
    subgraph P1["💥 Part 1 — Foundations"]
        A1[01 Why AI Fails] --> A2[02 GEMINI.md]
        A2 --> A3[03 AGENTS.md]
        A3 --> A4[04 Smart Prompting]
    end

    subgraph P2["⚡ Part 2 — Power Tools"]
        B1[05 Custom Agents] --> B2[06 Commands]
        B2 --> B3[07 Hooks]
        B3 --> B4[08 Subagents]
    end

    subgraph P3["🐝 Part 3 — Agentic"]
        C1[09 Multi-Agent] --> C2[10 MCP Servers]
        C2 --> C3[11 Custom MCP]
        C3 --> C4[12 Ralph Loops]
    end

    subgraph P4["📋 Part 4 — Workflows"]
        D1[13 PRD Pipeline] --> D2[14 Plan Mode]
        D2 --> D3[15 Jira Integration]
    end

    A4 --> B1
    B4 --> C1
    C4 --> D1

    style P1 fill:#d4edda,stroke:#28a745
    style P2 fill:#cce5ff,stroke:#007bff
    style P3 fill:#e2d5f1,stroke:#6f42c1
    style P4 fill:#fff3cd,stroke:#ffc107
```

---

## 🕐 Time Map

| Part | Duration | Focus | You'll Build |
|------|----------|-------|-------------|
| 💥 **1. Foundations** | ~15 min | Memory + Prompting | GEMINI.md for TaskPulse |
| ⚡ **2. Power Tools** | ~12 min | Automation | Custom Agents, Commands, Hooks |
| 🐝 **3. Agentic** | ~12 min | Parallelism | Multi-Agent, MCP, Loops |
| 📋 **4. Workflows** | ~10 min | Process | PRD → Code Pipeline |
| 🎯 **5. Scenarios** | ~15 min | Daily Work | 10 Playbooks |

---

## 📖 Story Arc: Your First Month with AI

### 🗓️ Day 1 — "This AI keeps making mistakes"
→ [01 Why AI Fails](Part%201%20—%20Foundations/01-why-ai-fails.md) — Understand the root cause

### 🗓️ Day 2 — "Let me give it some context"
→ [02 GEMINI.md](Part%201%20—%20Foundations/02-gemini-md-basics.md) — Create project memory
→ [03 AGENTS.md](Part%201%20—%20Foundations/03-agents-md.md) — Universal config

### 🗓️ Day 3 — "My prompts are getting better"
→ [04 Smart Prompting](Part%201%20—%20Foundations/04-smart-prompting.md) — CRAC framework

### 🗓️ Week 1 — "I keep repeating myself"
→ [05 Custom Agents](Part%202%20—%20Power%20Tools/05-skills.md) — Auto-triggering patterns
→ [06 Commands](Part%202%20—%20Power%20Tools/06-commands.md) — Custom workflows

### 🗓️ Week 2 — "I need guardrails"
→ [07 Hooks](Part%202%20—%20Power%20Tools/07-hooks.md) — Automated checks
→ [08 Subagents](Part%202%20—%20Power%20Tools/08-subagents.md) — Parallel workers

### 🗓️ Week 3 — "I want AI to do more independently"
→ [09 Multi-Agent Delegation](Part%203%20—%20Agentic%20Patterns/09-agent-teams.md) — Multi-agent via shell tool delegation
→ [10 MCP Servers](Part%203%20—%20Agentic%20Patterns/10-mcp-servers.md) — External tools
→ [11 Custom MCP](Part%203%20—%20Agentic%20Patterns/11-custom-mcp.md) — Your own servers

### 🗓️ Week 4 — "AI is shipping features while I review"
→ [12 Ralph Loops](Part%203%20—%20Agentic%20Patterns/12-ralph-loops.md) — Autonomous loops
→ [13 PRD Pipeline](Part%204%20—%20Workflow%20Methodologies/13-prd-driven-dev.md) — Spec to code
→ [14 Plan Mode](Part%204%20—%20Workflow%20Methodologies/14-plan-mode-mastery.md) — Think first
→ [15 Jira Integration](Part%204%20—%20Workflow%20Methodologies/15-jira-integration.md) — Full loop

### 🗓️ Month 2+ — "I pick the right approach for each task"
→ [Part 5 Scenarios](Part%205%20—%20Daily%20Scenarios/) — Your daily playbook

---

## 🎯 Independence Scale

```mermaid
graph TD
    L1["🔴 Level 1: Copy-Paste Prompting<br/>You type everything, AI forgets everything"]
    L2["🟠 Level 2: Context-Aware<br/>GEMINI.md gives AI memory"]
    L3["🟡 Level 3: Tool-Augmented<br/>Custom Agents + Hooks reduce errors"]
    L4["🟢 Level 4: Agentic<br/>Multi-Agent + MCP work in parallel"]
    L5["🔵 Level 5: Autonomous<br/>Ralph Loops + PRD pipeline = AI ships features"]

    L1 --> L2 --> L3 --> L4 --> L5

    style L1 fill:#ffcccc
    style L2 fill:#ffe0cc
    style L3 fill:#ffffcc
    style L4 fill:#ccffcc
    style L5 fill:#cce5ff
```

> **Most teams are at Level 1-2.** This course takes you to **Level 5.**

---

## 🔀 Non-Linear Paths

Not everyone needs to follow the exact order. Here are shortcut paths:

### 🐛 "I mainly fix bugs"
01 → 02 → 04 → 07 (Hooks) → 19 (Bug Investigation) → 20 (Bug Fix)

### ✨ "I mainly build features"
01 → 02 → 04 → 05 (Custom Agents) → 09 (Multi-Agent) → 12 (Ralph Loops) → 21 (New Feature)

### 📋 "I mainly plan & spec"
01 → 02 → 04 → 13 (PRD Pipeline) → 14 (Plan Mode) → 15 (Jira) → 25 (Create PRD)

### 🔍 "I'm new to this codebase"
01 → 02 → 04 → 08 (Subagents) → 16 (Unknown Codebase) → 17 (RFC/SPIKE)

---

## 📎 Quick Links

| Need | Go To |
|------|-------|
| Copy-paste prompts | [B: Prompt Library](appendix/B-prompt-library.md) |
| Quick reference | [A: Cheat Sheet](appendix/A-cheat-sheet.md) |
| Links & docs | [C: Resources](appendix/C-resources.md) |

---

[🏠 README](README.md) | **You are here: 🗺️ Course Map** | [Start → 01 Why AI Fails](Part%201%20—%20Foundations/01-why-ai-fails.md)
