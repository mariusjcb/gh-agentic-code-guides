# 🪝 Hooks — Your AI's Guardrails

> **Time to read:** ~5 min | **Skill level:** Intermediate-Advanced | **Platform:** iOS & Android

GEMINI.md says "please do X." Skills teach patterns. But **hooks enforce rules.** They're deterministic shell scripts that run automatically on Gemini events.

> 💬 "Hooks catch what prompts miss."

Gemini forgot to lint? The hook runs it anyway. Gemini tried to commit to `main`? The hook blocks it. No exceptions.

---

## 🎯 What Are Hooks?

Hooks are **shell scripts that fire on specific Gemini events.** They're not AI-powered — they're plain old bash. That's the point.

| Layer | Type | Reliability |
|-------|------|-------------|
| GEMINI.md | Instructions | 🟡 Usually followed |
| Skills | Patterns | 🟡 Usually followed |
| **Hooks** | **Enforcement** | **🟢 Always runs** |

Hooks are your safety net. They don't *ask* Gemini to do something — they *make it happen*.

---

## ⚙️ Configuration

Hooks live in `.gemini/settings.json`:

```json
{
  "hooks": {
    "pre-tool": [
      {
        "matcher": "Bash",
        "command": "/path/to/your/script.sh"
      }
    ],
    "post-tool": [
      {
        "matcher": "Edit",
        "command": "/path/to/another-script.sh"
      }
    ]
  }
}
```

Each hook has:
- **Event type** — When it fires
- **Matcher** — Which tool triggers it (optional, filters by tool name)
- **Command** — The shell script to run

---

## 📋 Key Event Types

| Event | When It Fires | Best Use Case |
|-------|--------------|---------------|
| `pre-tool` | ⏮️ Before a tool runs | Block dangerous commands |
| `post-tool` | ⏭️ After a tool runs | Auto-lint, auto-format |
| `notification` | 🔔 Gemini sends notification | Custom alerts (Slack, sound) |
| `session-end` | 🛑 Session ends | Auto-restart loops! |

### 🆕 Additional Hook Types

Gemini CLI provides several hook types beyond what other AI coding tools offer:

| Event | When It Fires | Best Use Case |
|-------|--------------|---------------|
| `session-start` | 🚀 Session begins | Setup environment, check prerequisites |
| `pre-llm` | 🧠 Before LLM call | Modify or log prompts |
| `post-llm` | 🧠 After LLM responds | Log or validate responses |
| `pre-agent-loop` | 🔁 Before agent loop starts | Initialize loop state |
| `post-agent-loop` | 🔁 After agent loop ends | Cleanup, summarize results |
| `pre-compress` | 🗜️ Before context compression | Log or adjust compression behavior |
| `pre-tool-selection` | 🎯 Before tool is selected | Influence tool choice |

These additional hooks give you fine-grained control over every stage of the Gemini CLI pipeline.

---

## 🎯 Matchers

Matchers filter hooks to specific tools:

| Matcher | Fires When |
|---------|-----------|
| `Bash` | Any bash command runs |
| `Edit` | A file is edited |
| `Write` | A new file is written |
| `Read` | A file is read |
| *(empty)* | Every tool invocation |

You can also use regex patterns: `"matcher": "Bash|Edit|Write"` to match multiple tools.

---

## 🔢 Exit Codes — The Control System

Your hook script's exit code tells Gemini what to do:

| Exit Code | Meaning | Gemini's Reaction |
|-----------|---------|-------------------|
| `0` | ✅ Proceed | Tool runs / continues normally |
| `2` | 🚫 Block + message | Tool is blocked; stdout shown to Gemini as feedback |
| Other | ❌ Error | Hook is ignored, tool proceeds |

**Exit code `2` is your superpower.** It blocks the action AND tells Gemini *why*, so it can self-correct.

---

## 🔄 Hook Lifecycle

```mermaid
sequenceDiagram
    participant U as 👤 User Prompt
    participant G as 🤖 Gemini
    participant Pre as 🪝 pre-tool Hook
    participant T as 🔧 Tool
    participant Post as 🪝 post-tool Hook

    U->>G: "Edit TaskView.swift"
    G->>Pre: Check before Edit
    alt Hook exits 0
        Pre->>G: ✅ Proceed
        G->>T: Run Edit tool
        T->>Post: File edited
        Post->>Post: Run SwiftLint
        Post->>G: Lint results
    else Hook exits 2
        Pre->>G: 🚫 Blocked + reason
        G->>U: "Can't do that because..."
    end
```

---

## 📱 Mobile Hook Examples

Here's a full `.gemini/settings.json` for a TaskPulse mobile project:

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
      },
      {
        "matcher": "Write",
        "command": ".gemini/hooks/auto-lint.sh"
      }
    ]
  }
}
```

---

### 🚫 Block Commits to `main`

**`.gemini/hooks/block-main-commit.sh`**

```bash
#!/bin/bash

# Read the tool input from stdin
INPUT=$(cat)

# Check if this is a git commit command
if echo "$INPUT" | grep -q "git commit\|git push"; then
  BRANCH=$(git branch --show-current)
  if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
    echo "🚫 BLOCKED: Cannot commit/push directly to $BRANCH."
    echo "Create a feature branch first: git checkout -b feature/your-change"
    exit 2
  fi
fi

exit 0
```

---

### 🧹 Auto-Lint After Every Edit

**`.gemini/hooks/auto-lint.sh`**

```bash
#!/bin/bash

# Read tool output from stdin
INPUT=$(cat)

# Extract the file path from the tool input
FILE=$(echo "$INPUT" | grep -oP '"file_path":\s*"\K[^"]+')

if [[ -z "$FILE" ]]; then
  exit 0
fi

# Swift files → SwiftLint
if [[ "$FILE" == *.swift ]]; then
  if command -v swiftlint &> /dev/null; then
    RESULT=$(swiftlint lint --path "$FILE" --quiet 2>&1)
    if [[ -n "$RESULT" ]]; then
      echo "⚠️ SwiftLint issues in $FILE:"
      echo "$RESULT"
    fi
  fi
fi

# Kotlin files → detekt / ktlint
if [[ "$FILE" == *.kt ]]; then
  if command -v ktlint &> /dev/null; then
    RESULT=$(ktlint "$FILE" 2>&1)
    if [[ -n "$RESULT" ]]; then
      echo "⚠️ ktlint issues in $FILE:"
      echo "$RESULT"
    fi
  fi
fi

exit 0
```

---

### 🎨 Auto-Format on Save

**`.gemini/hooks/auto-format.sh`**

```bash
#!/bin/bash

INPUT=$(cat)
FILE=$(echo "$INPUT" | grep -oP '"file_path":\s*"\K[^"]+')

if [[ -z "$FILE" ]]; then
  exit 0
fi

# Swift → swift-format
if [[ "$FILE" == *.swift ]]; then
  if command -v swift-format &> /dev/null; then
    swift-format format --in-place "$FILE" 2>&1
    echo "✅ Formatted $FILE with swift-format"
  fi
fi

# Kotlin → ktlint
if [[ "$FILE" == *.kt ]]; then
  if command -v ktlint &> /dev/null; then
    ktlint --format "$FILE" 2>&1
    echo "✅ Formatted $FILE with ktlint"
  fi
fi

exit 0
```

---

### 🧪 Run Tests Before Git Commit

**`.gemini/hooks/pre-commit-tests.sh`**

```bash
#!/bin/bash

INPUT=$(cat)

# Only trigger on git commit commands
if ! echo "$INPUT" | grep -q "git commit"; then
  exit 0
fi

echo "🧪 Running quick test suite before commit..."

# Check which files are staged
SWIFT_CHANGED=$(git diff --cached --name-only -- '*.swift' | wc -l)
KT_CHANGED=$(git diff --cached --name-only -- '*.kt' | wc -l)

if [ "$SWIFT_CHANGED" -gt 0 ]; then
  echo "Running iOS unit tests..."
  xcodebuild test \
    -workspace TaskPulse.xcworkspace \
    -scheme TaskPulse \
    -destination 'platform=iOS Simulator,name=iPhone 16,OS=latest' \
    -quiet 2>&1
  if [ $? -ne 0 ]; then
    echo "🚫 iOS tests failed. Fix tests before committing."
    exit 2
  fi
fi

if [ "$KT_CHANGED" -gt 0 ]; then
  echo "Running Android unit tests..."
  ./gradlew testDebugUnitTest --quiet 2>&1
  if [ $? -ne 0 ]; then
    echo "🚫 Android tests failed. Fix tests before committing."
    exit 2
  fi
fi

echo "✅ All tests passed."
exit 0
```

---

## ⚠️ The Guardrail Stack

The three layers work together:

```
┌─────────────────────────────────────┐
│  🪝 Hooks (Enforcement)            │  ← "You WILL lint"
│  Always runs. Deterministic. Bash.  │
├─────────────────────────────────────┤
│  ⚡ Skills (Patterns)               │  ← "Here's HOW to write it"
│  Auto-loaded. AI-interpreted.       │
├─────────────────────────────────────┤
│  📝 GEMINI.md (Instructions)        │  ← "Here's WHAT we do"
│  Always present. Sets the tone.     │
└─────────────────────────────────────┘
```

| Layer | Strength | Weakness |
|-------|----------|----------|
| GEMINI.md | Broad guidance | Can be ignored by AI |
| Skills | Detailed patterns | Only loads when matched |
| **Hooks** | **Always executes** | **Only reacts, can't generate** |

**Use all three together.** GEMINI.md teaches. Skills demonstrate. Hooks enforce.

---

## 🧪 Try It Now

1. **Create a lint hook:** Set up `auto-lint.sh` to run SwiftLint or ktlint after every file edit. Watch Gemini get instant feedback.

2. **Block `main` commits:** Add the branch protection hook. Try to commit to `main` and see it blocked.

3. **Test exit code 2:** Write a hook that blocks any file write to `Sources/Generated/` (auto-generated files shouldn't be hand-edited). Verify Gemini gets the message and adjusts.

4. **Build the full stack:** For one feature in TaskPulse, set up:
   - GEMINI.md rule: "All views must have previews"
   - Skill: SwiftUI component generator with preview template
   - Hook: Script that checks `#Preview` exists after `.swift` file creation

   See how all three layers reinforce each other.

---

← Previous: [06-commands.md](06-commands.md) | [🗺️ Course Map](../00-course-map.md) | Next →: [08-subagents.md](08-subagents.md)
