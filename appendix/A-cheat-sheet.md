# 📋 Quick Reference Cheat Sheet

> **Print this. Pin it. Refer to it daily.**

---

## 🗂️ File Locations

| File | Path | Purpose |
|------|------|---------|
| **CLAUDE.md** | `./CLAUDE.md` | Project-wide AI memory (architecture, rules, patterns) |
| **AGENTS.md** | `./AGENTS.md` | Multi-agent role definitions and coordination |
| **Skills** | `.claude/skills/*/SKILL.md` | Auto-triggered reusable instruction sets |
| **Commands** | `.claude/commands/*.md` | On-demand slash commands |
| **Settings** | `.claude/settings.json` | Tool permissions, MCP servers, model config |
| **User Settings** | `~/.claude/settings.json` | Global user-level preferences |
| **MCP Config** | `.claude/settings.json` → `mcpServers` | MCP server connections |
| **Hooks** | `.claude/settings.json` → `hooks` | Pre/post event triggers |

---

## 🛠️ Tool Decision Matrix

*"Which tool do I use for this?"*

| Need | Tool | Why | Set Up Time |
|------|------|-----|-------------|
| 🧠 Consistent output every session | **CLAUDE.md** | Persistent memory across sessions | 10 min |
| 🔁 Repeated pattern (auto-triggered) | **Skill** | Loads automatically when relevant | 15 min |
| 🎯 Manual workflow (on-demand) | **Command** | Triggered with `/slash` commands | 10 min |
| 🛡️ Error prevention (deterministic) | **Hook** | Runs scripts before/after actions | 20 min |
| 🔍 Quick focused research | **Subagent** | Scoped context, doesn't pollute main chat | 0 min |
| 👥 Parallel independent work | **Agent Team** | Multiple agents work simultaneously | 5 min |
| 🔌 External data / tools | **MCP Server** | Standardized protocol for integrations | 30 min |
| 🤖 Full autonomous implementation | **Ralph Loop** | Self-correcting build→test→fix cycle | 5 min |

### Quick Decision Flowchart

```mermaid
flowchart TD
    A["What do I need?"] --> B{"Happens every session?"}
    B -->|Yes| C{"Automatic or manual?"}
    B -->|No| D{"External data?"}

    C -->|Automatic| E["⚡ Skill"]
    C -->|Manual| F["🎯 Command"]

    D -->|Yes| G["🔌 MCP Server"]
    D -->|No| H{"Parallel work?"}

    H -->|Yes| I["👥 Agent Team"]
    H -->|No| J{"Self-correcting?"}

    J -->|Yes| K["🔄 Ralph Loop"]
    J -->|No| L["🔍 Subagent"]
```

---

## ⌨️ Key Shortcuts & Commands

| Shortcut / Command | What It Does |
|---------------------|-------------|
| `Shift+Tab` | Toggle **Plan Mode** (thinking without coding) |
| `/init` | Initialize CLAUDE.md for current project |
| `/compact` | Compress conversation context to save tokens |
| `Escape` | Cancel current AI generation |
| `#` (in prompt) | Reference a file path |
| `@` (in prompt) | Reference a person/agent |

### CLI Flags

```bash
# Headless mode (no interactive UI)
claude --headless -p "Your prompt here"

# Specify model
claude --model claude-sonnet-4-20250514

# Resume last conversation
claude --continue

# Print output only (for piping)
claude --print -p "Your prompt"
```

---

## 📋 CRAC Prompt Template

**C**ontext → **R**ole → **A**ction → **C**onstraints

```
[CONTEXT]
I'm working on TaskPulse, a collaborative task manager.
iOS: SwiftUI + MVVM. Android: Compose + MVVM.
Current file: Sources/Features/Tasks/TaskListViewModel.swift

[ROLE]
You are a senior iOS engineer reviewing this code.

[ACTION]
Add pagination to the task list: load 20 tasks at a time,
fetch next page when user scrolls to bottom.

[CONSTRAINTS]
- Use async/await (no Combine)
- Follow existing patterns in TaskListViewModel
- Don't modify TaskService interface
- Include unit tests
```

### Quick CRAC Examples

| Scenario | Context | Role | Action | Constraints |
|----------|---------|------|--------|-------------|
| New screen | "TaskPulse, MVVM" | "Senior iOS dev" | "Create task detail screen" | "Match existing patterns" |
| Debug | "Crash on line 42" | "Debugger" | "Find root cause" | "Don't change public API" |
| Refactor | "Module has 800 lines" | "Architect" | "Split into smaller files" | "No behavior changes" |
| Review | "PR #234" | "Code reviewer" | "Review for issues" | "Focus on performance" |

---

## 📊 Key Stats to Remember

| Stat | Value | Source |
|------|-------|--------|
| AI bug rate (no guardrails) | **1.7x more** than human-only | CodeRabbit 2025 |
| AI bug rate (with guardrails) | **25% fewer** than human-only | CodeRabbit 2025 |
| AI security vulnerabilities | **1.5-2x more** without rules | GitClear Analysis |
| Devs who won't merge AI code unreviewed | **71%** | Stack Overflow Survey |
| AI-generated code with defects | **41%** | Microsoft Research |
| Context window utilization sweet spot | **60-70%** | Anthropic Guidance |

---

## 🔄 The Ralph Loop (Quick Reference)

```
Implement [TASK]. After each change:
1. Build: [BUILD_COMMAND]
2. If build fails → read error → fix → rebuild
3. Test: [TEST_COMMAND]
4. If test fails → read failure → fix → retest
5. Repeat until green
6. Stop and ask if: [ESCALATION_CONDITIONS]
```

---

## 🏗️ Agent Team Template

```
Launch [N] subagents:

Agent 1 — "[ROLE]": [TASK]. Files: [SCOPE]
Agent 2 — "[ROLE]": [TASK]. Files: [SCOPE]
Agent 3 — "[ROLE]": [TASK]. Files: [SCOPE]

Coordination: [SHARED_INTERFACE_OR_CONTRACT]
Each agent: build-verify independently before reporting back.
```

---

## 📱 Mobile Quick Reference

### iOS Build Commands
```bash
# Build
swift build
xcodebuild -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16'

# Test
swift test
xcodebuild test -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16'
```

### Android Build Commands
```bash
# Build
./gradlew assembleDebug

# Test
./gradlew testDebugUnitTest

# Lint
./gradlew lintDebug
```

---

## 🎯 The Golden Rules

1. **Context is everything** — CLAUDE.md > clever prompting
2. **Plan before code** — Shift+Tab saves hours of rework
3. **Verify every step** — Ralph Loop: build, test, fix, repeat
4. **Understand before changing** — Explore → impact analysis → implement
5. **Skills > repeating yourself** — If you've said it 3x, make it a skill
6. **Flags > big-bang launches** — Ship safely with feature flags
7. **Docs travel with code** — Doc-as-code, not doc-in-wiki

---

← [🗺️ Course Map](../00-course-map.md) | Next →: [B — Prompt Library](B-prompt-library.md)
