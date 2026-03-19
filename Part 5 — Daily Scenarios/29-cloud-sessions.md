# ☁️ Claude Code on the Web — Cloud Sessions

> **Time to read:** ~5 min | **Skill level:** Intermediate | **Platform:** Any (browser-based)

Everything you've learned so far runs in your **local terminal.** But Claude Code also runs in the **cloud** — no local setup, no environment hassles, accessible from any device.

> "Start a coding task from your phone at lunch. Pick it up on your laptop when you're back."

---

## 🎯 What Are Cloud Sessions?

Cloud sessions run Claude Code on **Anthropic-managed infrastructure.** You connect a GitHub repo, give Claude a task, and it works in a sandboxed environment — reading files, running commands, creating PRs — all without touching your local machine.

Access it at: **`claude.ai/code`**

| Feature | Local CLI | Cloud Sessions |
|---------|----------|----------------|
| Where it runs | Your machine | Anthropic's cloud |
| Setup required | Install CLI, configure env | None (browser only) |
| Access your files | Directly | Via GitHub connection |
| Output | Terminal | Web UI + PR |
| Plan required | API key or subscription | Pro, Max, or Enterprise |
| Best for | Interactive dev work | Kick-off tasks, parallel work |

---

## 🚀 Getting Started

### From the Web

1. Go to **`claude.ai/code`**
2. Connect your GitHub repository
3. Describe your task
4. Claude clones, works, and creates a PR

### From the CLI

Use the `--remote` flag to create a cloud session from your terminal:

```bash
# Start a cloud session for the current repo
claude --remote

# Start with a specific task
claude --remote -p "Add pagination to the TaskList feature"
```

The `--remote` flag creates a session on Anthropic's infrastructure. You get a URL to monitor progress in your browser.

---

## 📱 Multi-Task Parallel Execution

The killer feature of cloud sessions: **run multiple tasks simultaneously.**

From your browser, kick off several independent tasks:

```
Session 1: "Implement dark mode toggle in Settings"
Session 2: "Add unit tests for CommentRepository"
Session 3: "Migrate TaskListViewModel from Combine to async/await"
```

Each session runs in its own isolated environment. No conflicts. No waiting. Three PRs ready for review.

---

## 🪝 SessionStart Hooks

When Claude starts a cloud session, it clones your repo into a fresh environment. It doesn't have your local tools installed. **SessionStart hooks** fix this.

Add a `SessionStart` hook to `.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "command": ".claude/hooks/setup-environment.sh"
      }
    ]
  }
}
```

### Environment Setup Script

**`.claude/hooks/setup-environment.sh`**

```bash
#!/bin/bash
# Runs when a cloud session starts — installs dependencies and tools

echo "Setting up TaskPulse environment..."

# Detect platform
if [[ -f "Package.swift" ]]; then
    echo "iOS project detected"
    swift package resolve
    # Install SwiftLint if available
    if command -v brew &> /dev/null; then
        brew install swiftlint 2>/dev/null || true
    fi
fi

if [[ -f "build.gradle" ]] || [[ -f "build.gradle.kts" ]]; then
    echo "Android project detected"
    ./gradlew dependencies --quiet 2>/dev/null || true
fi

# Install project-specific tools
if [[ -f "package.json" ]]; then
    npm install
fi

echo "Environment ready."
```

### What SessionStart Hooks Are For

| Use Case | Example |
|----------|---------|
| Install dependencies | `npm install`, `swift package resolve` |
| Set up linters | Install SwiftLint, ktlint |
| Configure environment | Set env vars, create config files |
| Verify prerequisites | Check required tools are available |
| Seed test data | Set up local database for tests |

---

## 🔄 Local vs. Cloud — When to Use Each

### Use Local CLI When:
- You're actively developing and want instant feedback
- You need access to iOS Simulator or Android Emulator
- You're debugging with breakpoints and live logs
- You need your full local toolchain (Xcode, Android Studio)
- You want interactive conversation with Claude

### Use Cloud Sessions When:
- You want to delegate a task and come back later
- You need to run multiple independent tasks in parallel
- You're on a device without your dev environment (phone, tablet, shared computer)
- You want to give Claude a task before bed and review the PR in the morning
- You're onboarding and don't have the project set up locally yet

### The Hybrid Workflow

```
Morning:
  ☁️ Cloud: "Implement the Comments feature per PLAN.md"
  ☁️ Cloud: "Add missing tests for UserRepository"

Meanwhile:
  💻 Local: Interactive bug investigation on a crash report

Afternoon:
  Review 2 PRs from cloud sessions
  💻 Local: Fine-tune the Comments UI based on PR review
```

---

## 🔐 Security & Isolation

Each cloud session runs in a **sandboxed environment**:

- Isolated filesystem — sessions can't access each other
- Network restrictions — only approved external connections
- No persistent state — environment is destroyed after session ends
- GitHub permissions — only repos you explicitly connect
- Secrets — never stored in session, passed via GitHub integration

---

## 🧪 Try It Now

1. **Start a cloud session.** Go to `claude.ai/code`, connect a repo, and give Claude a small task (e.g., "Add a README section about the project architecture").

2. **Try `--remote` from CLI.** Run `claude --remote -p "List all TODO comments in the codebase"` and watch the web UI show progress.

3. **Set up a SessionStart hook.** Create `.claude/hooks/setup-environment.sh` with your project's dependency installation. Commit it so cloud sessions auto-configure.

4. **Run parallel tasks.** Start 2 cloud sessions with independent tasks. See both PRs arrive without conflicts.

5. **Compare with local.** Do the same task locally and in the cloud. Note the tradeoffs: local is faster for iteration, cloud is better for delegation.

---

← [Previous: Create PRD](25-create-prd.md) | [Course Map](../00-course-map.md) | [Appendix A: Cheat Sheet →](../appendix/A-cheat-sheet.md)
