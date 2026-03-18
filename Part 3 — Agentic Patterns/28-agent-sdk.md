# 🧱 Claude Agent SDK — Build Your Own Agents

> **Time to read:** ~5 min | **Skill level:** Advanced | **Platform:** Python & TypeScript

You've used Claude Code's built-in agents (subagents, agent teams). Now build **your own** standalone agents with the Claude Agent SDK.

> MCP = connect Claude to external tools. Agent SDK = build autonomous programs powered by Claude.

---

## 🎯 What Is It?

The **Claude Agent SDK** (formerly "Claude Code SDK") is a library for building AI agents that can:

- Read, write, and edit files
- Execute shell commands
- Search the web
- Make decisions autonomously
- Run in loops until a task is complete

Available in **Python** and **TypeScript**:

```bash
# Python (3.10+)
pip install claude-agent-sdk

# TypeScript / JavaScript
npm install @anthropic-ai/claude-agent-sdk
```

---

## 💡 MCP SDK vs. Agent SDK

You've already learned about MCP in modules 10-11. Here's how Agent SDK differs:

| | MCP SDK | Agent SDK |
|---|---------|-----------|
| **Purpose** | Build tool servers Claude connects to | Build autonomous agents powered by Claude |
| **Direction** | Claude calls YOUR tools | YOUR code calls Claude |
| **Use case** | "Give Claude access to Jira" | "Build a bot that triages Jira tickets" |
| **Output** | A server Claude uses | A standalone program |
| **Module** | [10-mcp-servers](10-mcp-servers.md), [11-custom-mcp](11-custom-mcp.md) | This module |

Think of it this way:
- **MCP**: You build a restaurant menu (tools). Claude is the customer who orders.
- **Agent SDK**: You build a chef (agent). Claude is the brain that decides what to cook.

---

## 🐍 Python Quickstart

### Basic Agent

```python
from claude_agent_sdk import Agent, AgentConfig

agent = Agent(
    config=AgentConfig(
        model="claude-sonnet-4-6",
        max_tokens=4096,
    )
)

# Run a simple task
result = agent.run(
    "Read the file Sources/Features/TaskList/TaskListViewModel.swift "
    "and suggest 3 performance improvements."
)
print(result.output)
```

### Agent with Tool Restrictions

```python
from claude_agent_sdk import Agent, AgentConfig, Permissions

agent = Agent(
    config=AgentConfig(
        model="claude-sonnet-4-6",
        permissions=Permissions(
            allow_read=True,
            allow_write=False,      # Read-only agent
            allow_bash=False,       # No shell access
            allow_web_search=True,
        ),
    )
)

result = agent.run(
    "Analyze the project structure and create an architecture diagram "
    "in markdown format."
)
```

### Agent with Hooks

```python
from claude_agent_sdk import Agent, AgentConfig, Hook, HookResult

def block_dangerous_commands(tool_name: str, tool_input: dict) -> HookResult:
    """PreToolUse hook that blocks destructive bash commands."""
    if tool_name == "Bash":
        command = tool_input.get("command", "")
        dangerous = ["rm -rf", "git push --force", "drop table"]
        for pattern in dangerous:
            if pattern in command.lower():
                return HookResult.block(
                    f"Blocked dangerous command: {pattern}"
                )
    return HookResult.allow()

agent = Agent(
    config=AgentConfig(
        model="claude-sonnet-4-6",
        hooks=[
            Hook(
                event="PreToolUse",
                handler=block_dangerous_commands,
            )
        ],
    )
)
```

---

## 📦 TypeScript Quickstart

```typescript
import { Agent, AgentConfig } from '@anthropic-ai/claude-agent-sdk';

const agent = new Agent({
  model: 'claude-sonnet-4-6',
  maxTokens: 4096,
});

const result = await agent.run(
  'Read all *.swift files in Sources/Features/ and list any ViewModels ' +
  'missing @MainActor annotation.'
);

console.log(result.output);
```

### TypeScript with Hooks

```typescript
import { Agent, Hook, HookResult } from '@anthropic-ai/claude-agent-sdk';

const auditHook: Hook = {
  event: 'PostToolUse',
  handler: (toolName, toolInput, toolOutput) => {
    console.log(`[AUDIT] ${toolName}: ${JSON.stringify(toolInput).slice(0, 100)}`);
    return HookResult.allow();
  },
};

const agent = new Agent({
  model: 'claude-sonnet-4-6',
  hooks: [auditHook],
});
```

---

## 📱 Mobile Dev Use Cases

### 1. Test Generator Agent

```python
agent = Agent(config=AgentConfig(model="claude-sonnet-4-6"))

result = agent.run("""
Scan Sources/Features/ for all ViewModels.
For each ViewModel that doesn't have a corresponding test file
in Tests/Features/, generate one following existing test patterns.

Rules:
- Use XCTest + Swift Testing
- Test loading, success, error, and empty states
- Use existing MockTaskService from TestHelpers/
- Run tests after generating each file
""")
```

### 2. Migration Agent

```python
result = agent.run("""
Migrate all Combine-based ViewModels to async/await.
For each file:
1. Replace Publishers with async functions
2. Replace sink with await calls
3. Replace @Published + Combine with @Published + Task
4. Run tests to verify behavior is preserved
5. Track progress in MIGRATION_LOG.md
""")
```

### 3. PR Review Bot

```python
import subprocess

# Get the PR diff
diff = subprocess.run(
    ["gh", "pr", "diff", "42"],
    capture_output=True, text=True
).stdout

result = agent.run(f"""
Review this PR diff for TaskPulse:

{diff}

Check for:
- Missing @MainActor annotations on ViewModels
- Force unwraps in production code
- Missing unit tests for new functions
- Architecture violations (View calling Repository directly)

Output as GitHub-flavored markdown with file:line references.
""")

# Post as PR comment
subprocess.run(
    ["gh", "pr", "comment", "42", "--body", result.output]
)
```

---

## 🔗 Agent SDK + Ralph Loops

Combine the Agent SDK with Ralph Loop patterns for fully autonomous workflows:

```python
from claude_agent_sdk import Agent, AgentConfig

agent = Agent(config=AgentConfig(model="claude-sonnet-4-6"))

max_iterations = 5
for i in range(max_iterations):
    result = agent.run(f"""
    Iteration {i+1}/{max_iterations}.
    Continue implementing the Comments feature.
    Check PROGRESS.md for current status.
    Run tests. Fix failures.
    Write 'STATUS: DONE' to PROGRESS.md when all tests pass.
    """)

    with open("PROGRESS.md") as f:
        if "STATUS: DONE" in f.read():
            print(f"Complete after {i+1} iterations!")
            break
```

---

## ⚠️ When to Use What

| You want to... | Use |
|----------------|-----|
| Give Claude access to external tools | MCP Server (Module 10-11) |
| Run Claude in your terminal interactively | Claude Code CLI |
| Run Claude in CI/CD pipelines | GitHub Action (Module 26) |
| Build a standalone bot/automation | **Agent SDK** |
| Build a custom code review tool | Agent SDK + GitHub API |
| Create a Slack bot powered by Claude | Agent SDK + Slack API |

---

## 🧪 Try It Now

1. **Install the SDK.** `pip install claude-agent-sdk` or `npm install @anthropic-ai/claude-agent-sdk`.

2. **Build a read-only agent.** Create an agent that scans your project and reports architecture violations. No write permissions — safe to experiment.

3. **Add a hook.** Implement a PreToolUse hook that logs every tool call. Run your agent and review the audit log.

4. **Build a test generator.** Point your agent at a module with missing tests. Let it generate test files. Review and keep the good ones.

5. **Combine with a Ralph Loop.** Wrap your agent in a Python loop. Give it a feature to implement. Watch it iterate to completion.

---

← [Previous: Ralph Loops](12-ralph-loops.md) | [Course Map](../00-course-map.md) | [Next: PRD Pipeline →](../Part%204%20—%20Workflow%20Methodologies/13-prd-driven-dev.md)
