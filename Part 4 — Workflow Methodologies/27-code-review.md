# 🔍 Automated Code Review — Claude Reviews Every PR

> **Time to read:** ~5 min | **Skill level:** Intermediate | **Platform:** iOS & Android

You already have a `/review-pr` command. But what if Claude reviewed **every PR automatically** — posting inline comments on specific lines, catching logic errors, and flagging security issues before any human looks at it?

That's Claude Code's native **Automated Code Review.**

---

## 🎯 What Is It?

Claude Code can be configured to **automatically review pull requests** in your GitHub repositories. It posts inline comments on specific lines of code, looking for:

- Logic errors and off-by-one bugs
- Security vulnerabilities (injection, auth bypass, data exposure)
- Broken edge cases
- Subtle regressions
- Architecture violations (based on your CLAUDE.md)

It's not a linter. It's an AI code reviewer that understands context.

---

## ⚙️ Setup

### Step 1: Enable Code Review (Admin)

1. Go to `claude.ai/admin-settings/claude-code`
2. Enable **Code Review** for your organization
3. Select which repositories Claude should monitor
4. Choose review triggers

### Step 2: Choose Review Triggers

| Trigger | When Claude Reviews | Best For |
|---------|-------------------|----------|
| After PR creation | When a new PR is opened | Catch issues early |
| After every push | On each commit pushed to a PR | Continuous review |
| On `@claude review` | When someone comments `@claude review` | On-demand reviews |

### Step 3: Customize with REVIEW.md

Create a `REVIEW.md` at your repo root to customize what Claude flags:

```markdown
# Review Guidelines

## Always Flag
- Force unwraps (!) in Swift production code
- GlobalScope usage in Kotlin
- Missing error handling in async functions
- Direct database calls from ViewModels
- Hardcoded strings that should be localized

## Ignore
- Test files (Tests/**) — relaxed rules for mocks
- Generated files (*.generated.swift, *.generated.kt)
- Build configuration files

## Architecture Rules
- Views must not import Repository layer directly
- ViewModels must be @MainActor (Swift) or use viewModelScope (Kotlin)
- All network calls go through NetworkService protocol

## Severity Levels
- CRITICAL: Security issues, data loss risks, crash potential
- WARNING: Performance issues, code smell, missing tests
- SUGGESTION: Style improvements, better naming, documentation
```

---

## 📱 Mobile-Specific Review Config

### TaskPulse `REVIEW.md`

```markdown
# TaskPulse Code Review

## iOS — Must Flag
- Missing @MainActor on ViewModels
- Combine usage (we migrated to async/await)
- UIKit imports in SwiftUI views
- Missing SwiftUI #Preview blocks
- Force unwraps in non-test code

## Android — Must Flag
- LiveData usage (we use StateFlow)
- Missing sealed interface for UiState
- GlobalScope.launch anywhere
- Direct Dispatchers.IO usage (use injected dispatcher)
- Missing @Composable Preview annotations

## Cross-Platform
- API endpoints without error mapping to AppError
- Repository methods without unit tests
- Files exceeding 200 lines
- PRs exceeding 400 changed lines (suggest splitting)

## Path-Specific Rules
- Sources/Networking/: Extra scrutiny on auth token handling
- Sources/Features/Chat/: All messages must use MessageEncryptionService
- app/src/main/java/**/data/: Verify Room migrations
```

---

## 🔄 Review Modes in Practice

### Mode 1: Fully Automatic

Claude reviews every PR without being asked:

```
PR opened → Claude analyzes diff → Inline comments posted → Human reviews
```

Best for: Teams that want a first-pass review on everything.

### Mode 2: On-Demand

Humans trigger reviews when they want a second opinion:

```
Developer opens PR → Teammate comments "@claude review" → Claude reviews
```

Best for: Teams that want human review first, AI review as backup.

### Mode 3: Hybrid

Auto-review for certain paths, on-demand for others:

```
Swift file changed → Auto-review with iOS rules
Build config changed → Skip (no auto-review)
"@claude review security" → Deep security-focused review
```

---

## 📊 What Claude's Review Looks Like

Claude posts inline comments directly on PR diffs:

```
📍 Sources/Features/Tasks/TaskListViewModel.swift:42

⚠️ WARNING: This `Task {}` block captures `self` strongly.
Since `TaskListViewModel` is a class, this creates a retain cycle.

Suggestion: Use `[weak self]` capture:

  Task { [weak self] in
      guard let self else { return }
      await self.loadTasks()
  }
```

```
📍 app/src/main/java/.../TaskRepository.kt:78

🔴 CRITICAL: `withContext(Dispatchers.IO)` used directly.
Per REVIEW.md, use the injected dispatcher for testability:

  suspend fun getTasks(): List<Task> =
      withContext(ioDispatcher) { ... }
```

---

## 🆚 Native Review vs. `/review-pr` Command

| Feature | `/review-pr` Command | Native Code Review |
|---------|---------------------|-------------------|
| Trigger | Manual (type `/review-pr`) | Automatic or `@claude review` |
| Where it runs | Your terminal | GitHub/Cloud |
| Output | Terminal output | Inline PR comments |
| Customization | Command prompt | `REVIEW.md` file |
| Team visibility | Only you | Everyone on the PR |
| CI integration | No | Yes |

Use **both**: `/review-pr` for quick local checks before pushing, native review for team-wide automated coverage.

---

## 🧪 Try It Now

1. **Create a REVIEW.md.** Add your project's review rules — what to flag, what to ignore, severity levels.

2. **Enable code review.** If you have admin access, enable it at `claude.ai/admin-settings/claude-code`. Otherwise, ask your admin.

3. **Test with a deliberate PR.** Open a PR with a known issue (force unwrap, missing error handling). Verify Claude catches it.

4. **Compare with your `/review-pr` command.** Run the same PR through both. See where they overlap and where each adds unique value.

5. **Tune your REVIEW.md.** After a week of reviews, look at false positives. Add them to the "Ignore" section. Add missed issues to "Always Flag."

---

← [Previous: CI/CD Automation](26-ci-cd-automation.md) | [Course Map](../00-course-map.md) | [Next: Claude Agent SDK →](../Part%203%20—%20Agentic%20Patterns/28-agent-sdk.md)
