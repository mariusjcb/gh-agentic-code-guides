# Gemini CLI for Mobile Engineers -- Zero to Autonomous in 10 Minutes

> Everything you need to stop AI from hallucinating and start shipping with it.

**Contents:** [Why AI Fails](#1-why-ai-fails) | [Three Concepts](#2-three-concepts) | [Setup](#3-setup) | [Power Tools](#4-power-tools) | [Anti-Hallucination](#5-anti-hallucination-system) | [Daily Playbooks](#6-daily-playbooks) | [Advanced](#7-advanced-quick-reference) | [Golden Rules](#8-golden-rules)

---

## 1. Why AI Fails

| Metric | Value | Source |
|--------|-------|--------|
| AI bug rate (no guardrails) | **1.7x more** than human-only | CodeRabbit 2025 |
| AI-generated code with defects | **41%** | Microsoft Research |
| Devs who won't merge AI code unreviewed | **71%** | Stack Overflow Survey |
| AI bug rate (**with** guardrails) | **25% fewer** than human-only | CodeRabbit 2025 |

**Takeaway:** AI without context = chaos. AI with context + guardrails = superpower. This guide gives you both.

---

## 2. Three Concepts

### Context Engineering

AI forgets everything between sessions. `GEMINI.md` is your fix -- a markdown file at your project root that Gemini reads **automatically every session**. It's your AI's onboarding doc.

### Memory Engineering

LLMs reliably follow ~150 instructions. Keep `GEMINI.md` under 300 lines. For bigger projects, split with `@imports`:

```markdown
# In GEMINI.md
@.gemini/architecture.md
@.gemini/testing-conventions.md
@.gemini/platform-rules.md
```

### Prompt Engineering -- CRAC

Every prompt should have four parts:

| Part | What | Example |
|------|------|---------|
| **C**ontext | What AI needs to know | "SwiftUI + MVVM project, task list screen" |
| **R**ole | What expertise to bring | "Senior iOS engineer" |
| **A**ction | What to do | "Add pull-to-refresh" |
| **C**onstraints | Boundaries | "Only modify TaskListView.swift. Use async/await." |

Bad prompt vs good prompt:

```
BAD:  "Add pull to refresh to the task list"

GOOD:
[CONTEXT] TaskPulse iOS app. SwiftUI + MVVM. File: TaskListView.swift
[ROLE] Senior iOS engineer following existing patterns in Features/TaskList/
[ACTION] Add pull-to-refresh that calls TaskListViewModel.refresh()
[CONSTRAINTS]
- Only modify TaskListView.swift
- Use .refreshable modifier (async/await, not Combine)
- Follow existing error handling pattern in TaskListViewModel
- Include loading state
```

**Try it:** Pick your next task. Write a CRAC prompt before asking Gemini.

---

## 3. Setup

### GEMINI.md Template

Copy this, fill in your project details, save as `GEMINI.md` in your project root:

```markdown
# Project: [YOUR_PROJECT]
[One-line description]. Platform: [iOS SwiftUI / Android Compose / Both].
Architecture: [MVVM / MVI / VIPER]. DI: [Swinject / Hilt / Manual].

## Code Style
- Reference implementation: [path/to/best/feature/module/]
- ViewModels: Always @MainActor (Swift) / viewModelScope (Kotlin)
- No force unwraps (!) in Swift. No lateinit for UI state in Kotlin
- Max 200 lines per file. Feature-based modules, not layer-based

## Build & Test
# iOS
xcodebuild -scheme [SCHEME] -destination 'platform=iOS Simulator,name=iPhone 16' test
# Android
./gradlew :app:testDebugUnitTest

## Architecture
- Layers: View -> ViewModel -> Repository -> Service (never skip)
- Navigation: [Coordinator / NavigationStack / Navigation Component]
- Error handling: All errors map through [ErrorMapper / ErrorHandler]
- Tests: Given/When/Then. Use [MockService] from TestHelpers/

## Non-Goals
- Do NOT refactor unrelated code
- Do NOT add features not explicitly requested
- Do NOT use deprecated APIs: [list them]
```

### Folder Structure

```
your-project/
  GEMINI.md                          # Project memory (auto-loaded)
  AGENTS.md                          # Universal rules (works with Cursor, Copilot too)
  .geminiignore                      # Exclude noise from AI context
  .gemini/
    settings.json                    # Hooks, MCP servers, model config
    agents/                          # Custom specialist agents
      swiftui-component.md
      unit-test-writer.md
    commands/                        # Slash commands
      build-ios.toml
      test-module.toml
```

### .geminiignore

```
DerivedData/
.build/
build/
*.xcodeproj/xcuserdata/
Pods/
node_modules/
*.generated.swift
```

### AGENTS.md

Universal config that works across **all** AI coding tools (Gemini, Cursor, Copilot). Put shared architecture + style rules here. Put Gemini-specific features (hooks, agents, MCP) in `GEMINI.md`.

### CLI Commands

| Command | What It Does |
|---------|-------------|
| `/memory add "fact"` | Save a persistent fact across sessions |
| `/memory refresh` | Reload GEMINI.md after you edit it |
| `/compact` | Compress conversation to save tokens |
| `/plan` | Think-only mode (no code changes) |
| `/restore` | Roll back to a checkpoint |
| `!` | Toggle shell mode (run terminal commands) |
| `@path/to/file` | Inject file content into prompt |
| `@search "query"` | Fetch info from web/GitHub/docs |
| `gemini --headless` | Non-interactive mode (for scripts) |
| `gemini --continue` | Resume last conversation |

**Try it:** Copy the GEMINI.md template, fill in your project, start a Gemini session, ask: *"What architecture does this project use?"*

---

## 4. Power Tools

### Tool Decision Matrix

| Need | Tool | Setup |
|------|------|-------|
| Consistent output every session | **GEMINI.md** | 10 min |
| Repeated pattern (auto-triggered) | **Custom Agent** | 15 min |
| Manual workflow (on-demand) | **Slash Command** | 10 min |
| Error prevention (deterministic) | **Hook** | 20 min |
| Quick focused research | **Subagent** | 0 min |
| Parallel independent work | **Multi-Agent** | 5 min |
| External tools/data | **MCP Server** | 30 min |
| Full autonomous implementation | **Ralph Loop** | 5 min |

### Custom Agent

Auto-triggered specialists. Write once, Gemini uses them when relevant.

`.gemini/agents/swiftui-component.md`:
```markdown
---
name: swiftui-component
description: Generates SwiftUI views following project conventions
tools:
  - read
  - edit
  - write
  - bash
---

You are a SwiftUI specialist for this project.

When creating views:
- Follow patterns in Sources/Features/TaskList/ as reference
- Always use @MainActor on ViewModels
- Include SwiftUI previews with mock data
- Use design system tokens from UI/DesignSystem/
- Add accessibilityLabel to all interactive elements
- Max 150 lines per view file
```

Enable in `.gemini/settings.json`:
```json
{ "agents": { "swiftui-component": { "enabled": true } } }
```

**Try it:** Create an agent for your most repeated pattern. Ask Gemini to build a screen -- it auto-loads.

### Slash Command

On-demand tools triggered with `/command-name`.

`.gemini/commands/build-ios.toml`:
```toml
[command]
description = "Build iOS project and report errors"
prompt = """
Run the iOS build:
xcodebuild -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16,OS=18.0' build 2>&1

If it fails, read the error output and suggest fixes.
If it succeeds, report success with build time.
"""
```

`.gemini/commands/test-module.toml`:
```toml
[command]
description = "Run tests for a specific module"
prompt = """
Run tests for the specified module:
swift test --filter $ARGS

If any test fails, analyze the failure and suggest a fix.
"""
```

**Try it:** Create `/build-ios` and `/test-module`. Run `/build-ios` in your next session.

### Hook

Shell scripts that **enforce** rules. Not suggestions -- enforcement.

| Exit Code | Meaning |
|-----------|---------|
| `0` | Proceed normally |
| `2` | **Block action** + show feedback to Gemini |
| Other | Error (ignored) |

`.gemini/settings.json`:
```json
{
  "hooks": {
    "pre-tool": [
      {
        "matcher": "Bash",
        "command": ".gemini/hooks/block-main-commit.sh"
      }
    ],
    "post-tool": [
      {
        "matcher": "Edit",
        "command": ".gemini/hooks/auto-lint.sh"
      }
    ]
  }
}
```

`.gemini/hooks/block-main-commit.sh`:
```bash
#!/bin/bash
# Block commits to main/master
if echo "$GEMINI_TOOL_INPUT" | grep -q "git commit"; then
  BRANCH=$(git branch --show-current)
  if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
    echo "BLOCKED: Cannot commit directly to $BRANCH. Create a feature branch first."
    exit 2
  fi
fi
exit 0
```

`.gemini/hooks/auto-lint.sh`:
```bash
#!/bin/bash
# Auto-lint after every file edit
FILE="$GEMINI_TOOL_INPUT"
if [[ "$FILE" == *.swift ]]; then
  swiftlint lint --path "$FILE" --quiet 2>/dev/null
elif [[ "$FILE" == *.kt ]]; then
  ktlint "$FILE" 2>/dev/null
fi
exit 0
```

**Try it:** Add the block-main-commit hook. Try committing to main -- Gemini gets blocked.

### Subagent

Focused workers with their own context window. They investigate and report back without polluting your main session.

```
"Use a subagent to investigate the authentication module.
I need to know:
1. How tokens are stored and refreshed
2. What happens when a token expires mid-request
3. All entry points that require auth

Report back with a summary table."
```

**When to use:** Heavy codebase exploration, parallel investigation of multiple modules, focused code review passes (memory leaks, thread safety, accessibility -- each as a separate subagent).

### Multi-Agent Coordination

Multiple Gemini instances working in parallel via `gemini --headless`.

```
"Coordinate a team of specialists to implement the Chat feature:

Specialist 1 — UI Agent:
  Build SwiftUI views in Features/Chat/Views/
  Follow patterns from Features/TaskList/Views/

Specialist 2 — Data Agent:
  Build ChatRepository + ChatService in Features/Chat/Data/
  Follow patterns from Features/TaskList/Data/

Specialist 3 — Test Agent:
  Write unit tests in Tests/ChatTests/
  Follow patterns from Tests/TaskListTests/

Spawn each specialist via: gemini --headless with their task.
Each agent builds and verifies independently before reporting back.
Integration point: all agents share the ChatMessage model."
```

**Try it:** For your next feature, split it into UI + Data + Tests and run 3 agents in parallel.

### MCP Servers

Connect Gemini to external tools via Model Context Protocol.

`.gemini/settings.json`:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "ghp_your_token" }
    }
  }
}
```

Must-have MCP servers for mobile teams:

| Server | Use Case |
|--------|----------|
| **GitHub** | PR workflows, issue triage, code search |
| **Figma** | Design tokens, pixel-perfect UI implementation |
| **Firebase** | Push notifications, analytics, crash analysis |
| **Sentry** | Crash investigation, error tracking |
| **Jira/Linear** | Ticket-to-PR automation, status updates |

Add servers with: `gemini mcp add [server-name]`

**Try it:** Add the GitHub MCP server. Ask Gemini to list your open PRs.

### Ralph Loop

Autonomous improvement cycles. Give Gemini a task, walk away, come back to done.

`ralph.sh`:
```bash
#!/bin/bash
MAX_ITERATIONS=10
ITERATION=0

echo "# Ralph Loop Progress" > PROGRESS.md
echo "Status: IN_PROGRESS" >> PROGRESS.md

while [ $ITERATION -lt $MAX_ITERATIONS ]; do
    ITERATION=$((ITERATION + 1))
    echo "Iteration $ITERATION of $MAX_ITERATIONS"

    gemini --headless \
      "Continue implementing the [FEATURE] feature.

       Check PROGRESS.md for current status.
       1. Implement remaining code in Features/[FEATURE]/
       2. Build: [BUILD_COMMAND]
       3. Test: [TEST_COMMAND]
       4. Fix any failures
       5. Update PROGRESS.md with what you completed
       6. If ALL tests pass and feature is complete, write 'STATUS: DONE'

       ONLY modify files in Features/[FEATURE]/ and Tests/."

    if grep -q "STATUS: DONE" PROGRESS.md; then
        echo "Feature complete after $ITERATION iterations!"
        exit 0
    fi
done
echo "Hit max iterations. Check PROGRESS.md."
```

```bash
chmod +x ralph.sh && ./ralph.sh
```

**Try it:** Pick a small feature. Fill in the placeholders. Run it and watch Gemini iterate.

### Plan Mode

Think before coding. `/plan` enters a mode where Gemini generates architecture plans without writing code.

```
/plan

"I need to add offline caching to the task list.
Current: TaskListViewModel fetches from API every time.
Desired: Cache locally, show cached data instantly, sync in background.
Constraints: Use SwiftData (iOS) / Room (Android). No new dependencies."
```

Review the plan. Refine 2-3 times. Then approve and Gemini executes.

**Try it:** Before your next feature, start with `/plan`. Review the architecture before any code is written.

---

## 5. Anti-Hallucination System

### The Guardrail Stack

| Layer | Type | Reliability |
|-------|------|-------------|
| **Hooks** | Enforcement (shell scripts) | Always runs |
| **Agents** | Patterns (specialist instructions) | Usually followed |
| **GEMINI.md** | Memory (project rules) | Usually followed |
| **Prompting** | Requests (your words) | Variable |

### 10 Rules

1. **Reference existing code** -- "Follow patterns in `Features/TaskList/`" (not "use MVVM")
2. **Constrain scope** -- "ONLY modify files in `Features/Chat/`"
3. **Explicit non-goals** -- "Do NOT add caching, pagination, or analytics"
4. **Specify edge cases** -- "Handle: empty array, nil user, network timeout, 429 rate limit"
5. **Minimal changes** -- "Change ONLY TaskListViewModel.swift. No other files."
6. **Test first** -- "Write a failing test, then implement, then verify it passes"
7. **Build-verify loop** -- "After each change: build -> test -> fix -> repeat"
8. **Subagent isolation** -- Use subagents for heavy exploration (keeps main context clean)
9. **Track progress** -- "Update PROGRESS.md each iteration. Write STATUS: DONE when complete."
10. **Human skepticism** -- AI estimates are 2-3x optimistic. Verify every architectural claim against code.

### Worked Example: Finding ALL SLOs

When you need comprehensive results with zero misses (e.g., find all SLOs, update all FMEA entries, audit all API endpoints):

```
Step 1: "Use a subagent to grep the entire codebase for SLO-related
        patterns: 'SLO', 'service level', 'latency target', 'error budget',
        'availability', 'p99', 'p95'. Return every file and line number."

Step 2: "Cross-reference the subagent results against our monitoring config
        in infra/monitoring/ and our docs in docs/slos/. Are any missing?"

Step 3: "Create a markdown table: SLO name, current target, where it's
        defined, where it's monitored, gaps found."
```

The pattern: **search broadly -> cross-reference -> verify completeness -> present structured**.

---

## 6. Daily Playbooks

#### Unknown Codebase

**When:** New team, unfamiliar repo, legacy code.

1. Ask Gemini to explore and generate a GEMINI.md
2. Ask for a Mermaid architecture diagram
3. Ask to identify patterns in 2-3 key modules
4. Start with a small change to validate understanding

```
[CONTEXT] I just joined this project. Never seen this codebase before.
[ROLE] Senior mobile architect onboarding to a new team.
[ACTION] Explore this project and create a GEMINI.md with: project purpose,
architecture pattern, key directories, build commands, conventions, DI approach.
[CONSTRAINTS] Read at least 5 key files before writing. Be specific, not generic.
```

*Gotcha: Always review the generated GEMINI.md -- AI may guess wrong about conventions.*

#### RFC / SPIKE

**When:** Evaluating SDKs, comparing architectures, migration planning.

1. Define scope: specific questions + evaluation criteria + weights
2. Use subagents for parallel research (technical feasibility, team impact, cost)
3. Structure findings as RFC with decision matrix

```
[CONTEXT] Evaluating whether to adopt [FRAMEWORK] for [PROJECT].
Team: [N] iOS, [N] Android. Codebase: [SIZE] lines.
[ROLE] Technical architect conducting a structured SPIKE.
[ACTION] Research and produce an RFC with: options, weighted decision matrix
(velocity 25%, quality 20%, maintainability 20%, team expertise 20%, risk 15%),
and a clear recommendation.
[CONSTRAINTS] Include "do nothing" as an option. Timebox: today only.
```

*Gotcha: Define evaluation criteria BEFORE research starts to prevent bias.*

#### FMEA Analysis

**When:** Before launching features, security review, architecture changes.

1. Feed Gemini the PRD/RFC + relevant source code
2. Ask for failure modes across all layers (data, network, UI, security, platform)
3. Score each: Severity x Occurrence x Detection = RPN
4. RPN > 200 = must fix. RPN > 100 = should fix.

```
[CONTEXT] About to launch [FEATURE]. PRD: @docs/prd.md. Code: @Features/[FEATURE]/
[ROLE] Risk analyst performing FMEA on a mobile feature.
[ACTION] Identify 10+ failure modes. For each: Severity (1-10), Occurrence (1-10),
Detection (1-10), RPN = S*O*D, and mitigation plan.
[CONSTRAINTS] Cover: data corruption, network failures, UI stale state,
auth expiry, background task limits, memory pressure. Format as markdown table.
```

*Gotcha: AI defaults to medium scores. Challenge every rating with "why 5 and not 3 or 7?"*

#### Bug Investigation

**When:** Crashes, performance issues, flaky tests.

1. Gather evidence (crash log, stack trace, git log)
2. Ask for top 3 hypotheses with supporting/contradicting evidence
3. Validate each hypothesis methodically
4. Confirm root cause before fixing

```
[CONTEXT] Crash in [CLASS].[METHOD]. Stack trace: [PASTE]. Affects [N]% of users.
[ROLE] Senior debugger investigating a production crash.
[ACTION] Analyze the stack trace. Produce top 3 hypotheses ranked by likelihood.
For each: supporting evidence, contradicting evidence, how to verify.
[CONSTRAINTS] Do NOT fix anything yet. Investigation only.
```

*Gotcha: Don't let AI jump to fixing. Investigate first, fix second.*

#### Bug Fix

**When:** Root cause confirmed, fix needed.

1. `/plan` -- have Gemini analyze without touching code
2. Write failing test that proves the bug
3. Implement minimal fix
4. Run tests to verify

```
[CONTEXT] Confirmed bug: [DESCRIPTION]. Root cause: [CAUSE]. File: [PATH]
[ROLE] Senior iOS/Android debugger.
[ACTION] Write a regression test that reproduces this bug, then fix it.
[CONSTRAINTS] Only modify [FILE] and [TEST_FILE]. Smallest possible change.
No refactoring. No "while we're here" improvements.
```

*Gotcha: AI loves to "fix" bugs by refactoring. Pin it to minimal change.*

#### New Feature

**When:** Greenfield implementation.

1. Write a mini-PRD (5 min: what, why, acceptance criteria, non-goals)
2. `/plan` for architecture review
3. Break into 2-5 tickets if large (keep each under 150 instructions)
4. Use multi-agent for parallel implementation (UI + Data + Tests)
5. Ralph Loop for autonomous iteration
6. Review and merge

```
[CONTEXT] Building [FEATURE] for [PROJECT]. PRD: @docs/feature-prd.md
[ROLE] Tech lead coordinating feature implementation.
[ACTION] Break this into implementable tickets. For each: scope, files to create/modify,
acceptance criteria, test requirements.
[CONSTRAINTS] Max 5 tickets. Each ticket must be completable in one session.
No ticket should touch more than 3-5 files.
```

*Gotcha: Token ceiling -- <50 requirements = 95% accuracy, 150-300 = <50%. Keep tickets small.*

#### Update Feature

**When:** Extending existing functionality.

1. Read existing code first -- understand before changing
2. `/plan` with explicit scope boundaries
3. Implement with reference to existing patterns
4. Run existing tests + add new ones

```
[CONTEXT] Adding [CAPABILITY] to existing [FEATURE]. Current: @Features/[FEATURE]/
[ROLE] Senior developer extending existing code.
[ACTION] Add [CAPABILITY] following existing patterns exactly.
[CONSTRAINTS] Do NOT change existing public APIs. Do NOT rename files.
Add tests for new behavior. Existing tests must still pass.
```

*Gotcha: "Understand before changing" -- always read existing code first.*

#### Feature Flags

**When:** Safe rollout, A/B testing, kill switches.

1. Define flag in your feature flag system
2. Wrap new code paths behind the flag
3. Test both paths (flag on + flag off)

```
[CONTEXT] Shipping [FEATURE] behind a feature flag. Flag system: [LaunchDarkly/Firebase/Custom].
[ROLE] Senior mobile engineer implementing safe rollout.
[ACTION] Add feature flag [FLAG_NAME]. Wrap all new UI and logic behind it.
[CONSTRAINTS] Flag OFF = existing behavior exactly. Flag ON = new feature.
Test both states. No behavioral change to existing code when flag is OFF.
```

*Gotcha: Test the flag-OFF path. AI often forgets the old behavior still needs to work.*

#### Update Docs

**When:** Architecture changed, APIs evolved, onboarding gaps.

1. Identify what changed (git diff or manual list)
2. Ask Gemini to update docs to match current code
3. Verify docs against actual code

```
[CONTEXT] Recent changes: [LIST_CHANGES]. Docs that may be stale: @docs/
[ROLE] Technical writer updating documentation.
[ACTION] Compare docs to current code. Update all outdated sections.
[CONSTRAINTS] Only update what's factually wrong. Don't add opinions or
best practices that aren't in the code. Preserve existing doc structure.
```

*Gotcha: AI invents "best practices" in docs. Pin it to what the code actually does.*

#### Create PRD

**When:** New project, major feature, stakeholder alignment.

1. Brain dump: what, why, who, success metrics
2. Structure with Gemini into proper PRD format
3. Add non-goals (critical for preventing AI scope creep later)

```
[CONTEXT] Planning [FEATURE/PROJECT]. Brain dump:
- What: [DESCRIPTION]
- Why: [BUSINESS REASON]
- Users: [WHO]
- Success: [METRICS]
[ROLE] Product-minded engineer writing a technical PRD.
[ACTION] Structure this into a PRD with: context, user stories, technical requirements,
acceptance criteria, non-goals, constraints, and open questions.
[CONSTRAINTS] Non-goals section is mandatory. Include at least 5 non-goals.
Keep requirements atomic and testable.
```

*Gotcha: Non-goals are your best weapon against AI gold-plating. Be generous with them.*

---

## 7. Advanced Quick Reference

| Feature | How | When |
|---------|-----|------|
| **Checkpointing** | Automatic. `/restore` to roll back | Before risky changes |
| **Compress context** | `/compact` | Long sessions getting slow |
| **Shell mode** | `!` toggles terminal inside session | Quick terminal commands |
| **Web search** | `@search "query"` | Need docs or external info |
| **File injection** | `@path/to/file` or `@path/to/dir/` | Add specific context |
| **PTY support** | Run interactive commands (vim, git, REPLs) | Advanced terminal work |
| **Sandboxing** | `--sandbox` flag or Docker | Untrusted code execution |
| **Custom MCP** | Build when you need internal APIs/data | Proprietary tools, internal wikis |
| **Headless mode** | `gemini --headless -p "prompt"` | Scripts, CI/CD, Ralph Loops |

### Token Ceiling Rule (PRD Pipeline)

| Requirements Count | AI Accuracy |
|-------------------|-------------|
| < 50 | **95%+** |
| 50 - 150 | **85%+** |
| 150 - 300 | **< 50%** |

Break large features into RFCs (one area per RFC) -> tickets (2-5 per RFC). Keep each ticket under 50 requirements.

---

## 8. Golden Rules

1. **Context is everything** -- GEMINI.md beats clever prompting every time
2. **Plan before code** -- `/plan` saves hours of rework
3. **Verify every step** -- Build, test, fix, repeat. Trust but verify.
4. **Understand before changing** -- Explore -> impact analysis -> implement
5. **Agents over repeating yourself** -- Said it 3 times? Make it an agent
6. **Small scope, high accuracy** -- <50 requirements = 95% accuracy
7. **Non-goals prevent gold-plating** -- Tell AI what NOT to do, not just what to do
