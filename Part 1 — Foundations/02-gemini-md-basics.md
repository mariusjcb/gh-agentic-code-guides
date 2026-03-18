# 📝 Your First CLAUDE.md

> **One file. Infinite context. Every session.**

`CLAUDE.md` is a markdown file at your project root that Claude reads **automatically** at the start of every session. It's your AI's onboarding doc, style guide, and architecture reference — combined.

No more repeating yourself. No more "actually, we use MVVM here."

---

## 🚀 Quick Start

**30 seconds to your first CLAUDE.md:**

```bash
# In your project root
claude

# Then type:
/init
```

Claude scans your repo and generates a starter `CLAUDE.md`. It'll detect:
- 📦 Package manager (SPM, CocoaPods, Gradle)
- 🏗️ Project structure and modules
- 🧪 Test frameworks and patterns
- 🔧 Build commands

**Review it immediately.** The auto-generated version is a starting point — you'll customize it heavily.

---

## 📐 Anatomy of CLAUDE.md

Every good `CLAUDE.md` has these sections. Here's what each looks like for TaskPulse:

### 1️⃣ Project Context

```markdown
# Project: TaskPulse
Collaborative task manager with real-time sync.
iOS: SwiftUI + MVVM + SPM. Android: Jetpack Compose + MVVM + Gradle.
Modules: Core, Networking, Features, UI, Analytics.
```

**Why it matters:** Without this, AI doesn't know if it's building a game, a banking app, or a todo list. Tone, security requirements, and UX patterns all depend on this.

### 2️⃣ Code Style Rules

```markdown
## Code Style
- Swift: Follow Sources/Features/TaskList/ as the reference implementation
- Kotlin: Follow app/src/main/java/com/taskpulse/features/tasklist/ as reference
- ViewModels: Always @MainActor (Swift) or use viewModelScope (Kotlin)
- Naming: Use feature-based modules, not layer-based (Features/Chat/, not ViewModels/Chat/)
- No force unwraps (!) in Swift. No lateinit for UI state in Kotlin.
- All public APIs must have doc comments
```

### 3️⃣ Terminal Commands

```markdown
## Build & Test Commands
# iOS
xcodebuild -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16,OS=18.0' test
swift build --package-path Packages/Core

# Android
./gradlew :app:assembleDebug
./gradlew :app:testDebugUnitTest
./gradlew :features:task-list:testDebugUnitTest
```

**Why it matters:** AI can verify its own work by running builds and tests. Wrong commands = wasted cycles.

### 4️⃣ Key Project Info

```markdown
## Architecture
- MVVM with Coordinator pattern (iOS) / Navigation Component (Android)
- Repository pattern for data access
- Protocol/Interface-driven dependency injection
- Unidirectional data flow: View → ViewModel → Repository → Service
```

---

## 📏 The 300-Line Rule

Here's a constraint most people miss:

> Frontier LLMs reliably follow **150–200 instructions.** Claude's own system prompt uses ~50 of those slots. That leaves **~100–150 effective instructions** for your CLAUDE.md.

| Budget | Used By |
|--------|---------|
| ~200 total | LLM instruction-following capacity |
| ~50 | Claude's system prompt |
| **~150** | **Your CLAUDE.md budget** |

**Practical limit: ~300 lines of markdown** (comments, headers, and whitespace don't count as instructions).

Going over? Your later rules get ignored. The fix: **@imports.**

---

## 🔗 @imports — Splitting for Sanity

Break your CLAUDE.md into focused files. Only the relevant content loads per session.

```markdown
# In your root CLAUDE.md:
@.claude/architecture.md
@.claude/testing-conventions.md
@.claude/api-patterns.md
```

### TaskPulse Import Structure

```
.claude/
├── architecture.md        # MVVM layers, module boundaries, dependency graph
├── testing-conventions.md # XCTest/JUnit patterns, mock generation rules
├── api-patterns.md        # NetworkService protocol, endpoint conventions
├── swift-style.md         # iOS-specific code style
├── kotlin-style.md        # Android-specific code style
└── review-checklist.md    # PR review criteria
```

**`.claude/architecture.md` example:**

```markdown
## Module Dependency Graph
Core → Networking → Features → App
UI is shared across all Features modules.

## Layer Rules
- Views NEVER call Repository directly
- ViewModels NEVER import UIKit/Android View classes
- Repositories NEVER know about ViewModels
- Services are protocol/interface-based for testability
```

**`.claude/testing-conventions.md` example:**

```markdown
## Testing Rules
- Every ViewModel gets a corresponding test file
- Use MockTaskService (already exists in TestHelpers/)
- iOS: XCTest + Swift Testing. No third-party test frameworks.
- Android: JUnit5 + Turbine for Flow testing + MockK for mocks
- Minimum: test loading, success, and error states for every screen
```

---

## 🚫 .claudeignore

Keep AI focused. Exclude noise.

```gitignore
# .claudeignore
# iOS
DerivedData/
*.xcuserdata
Pods/
.build/

# Android
build/
.gradle/
*.apk
*.aab

# Shared
node_modules/
*.generated.swift
*.generated.kt
```

**Why?** Generated files confuse AI. Build artifacts waste context window. Pod/Gradle caches are noise.

---

## 📂 Nested CLAUDE.md

Module-level CLAUDE.md files **override** root rules for that directory.

```
TaskPulse/
├── CLAUDE.md                          # Root — global rules
├── Sources/
│   ├── Features/
│   │   ├── Chat/
│   │   │   └── CLAUDE.md             # Chat-specific rules
│   │   └── TaskList/
│   │       └── CLAUDE.md             # TaskList-specific rules
│   └── Networking/
│       └── CLAUDE.md                  # API layer rules
```

**`Features/Chat/CLAUDE.md` example:**

```markdown
# Chat Module Rules
- All messages must go through MessageEncryptionService before sending
- Use WebSocketManager (not raw URLSession) for real-time updates
- Message timestamps: always UTC, convert to local only in View layer
- Reference implementation: MessageBubbleView.swift for UI patterns
```

This is powerful. The Chat module has security rules that don't apply elsewhere. Nested CLAUDE.md keeps them scoped.

---

## 📋 Complete TaskPulse CLAUDE.md

Here's a production-ready example. **Copy and adapt this.**

```markdown
# TaskPulse — AI Coding Guide

## Project Overview
TaskPulse: collaborative task manager with real-time sync and offline support.
- iOS: SwiftUI, MVVM, Swift Package Manager, minimum iOS 17
- Android: Jetpack Compose, MVVM, Gradle with version catalogs, minimum API 28

## Architecture
- MVVM with unidirectional data flow
- iOS: Coordinator pattern for navigation, @MainActor on all ViewModels
- Android: Navigation Component, viewModelScope for all coroutines
- Repository pattern: ViewModel → Repository → (LocalDataSource + RemoteDataSource)
- Protocol/Interface-driven DI (no Swinject/Hilt magic unless in DI/ module)

## Module Structure
Core (models, utils) → Networking (API client) → Features (screens) → App (entry)
UI module is shared across all Features. Never duplicate UI components.

## Code Style — Swift
- No force unwraps. Use guard-let or if-let.
- Prefer async/await over Combine for new code
- All @Published properties require @MainActor on the class
- File naming: {Feature}{Layer}.swift (e.g., TaskListViewModel.swift)
- Reference implementation: Sources/Features/TaskList/

## Code Style — Kotlin
- Use StateFlow (not LiveData) for UI state
- Sealed interfaces for UiState: Loading, Success, Error
- viewModelScope.launch for all async work — never GlobalScope
- File naming: {Feature}{Layer}.kt (e.g., TaskListViewModel.kt)
- Reference implementation: features/task-list/src/main/java/.../

## Build & Test
# iOS
xcodebuild -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16,OS=18.0' test
swift test --package-path Packages/Core

# Android
./gradlew :app:assembleDebug
./gradlew :app:testDebugUnitTest --continue

## Testing Conventions
- Every ViewModel must have tests for: loading, success, error, and empty states
- iOS: XCTest + Swift Testing. Mocks in TestHelpers/ module.
- Android: JUnit5 + Turbine + MockK. Fakes in :core:testing module.
- No UI tests in ViewModel test files. Separate UI test targets.

## Error Handling
- All errors map to AppError enum/sealed class before reaching ViewModel
- Never show raw error messages to users
- Use ErrorMapper.map(error:) / ErrorMapper.map(throwable) helper

## PR & Review Rules
- Never commit code with warnings
- Run swiftlint (iOS) and ktlint (Android) before committing
- Max 400 lines per PR. Split larger changes into stacked PRs.

## Don't
- Don't use SwiftUI previews with live network calls
- Don't create god-objects: max 200 lines per file
- Don't add dependencies without discussing in PR description
- Don't use #if DEBUG for feature flags (use FeatureFlag service)

@.claude/api-patterns.md
@.claude/testing-conventions.md
```

**~80 lines. Under budget. Covers both platforms.**

---

## 🧪 Try It Now

1. **Create your CLAUDE.md.** Run `/init` in your project, then customize:
   - [ ] Add your project's architecture pattern
   - [ ] Add 3 code style rules AI keeps violating
   - [ ] Add your exact build/test commands
   - [ ] Add one "Don't" rule based on past AI mistakes

2. **Test it.** Start a new Claude session and ask: *"What architecture pattern does this project use?"* It should answer from your CLAUDE.md without looking at code.

3. **Extract one @import.** Move your testing conventions to `.claude/testing-conventions.md` and add the `@` import to your root CLAUDE.md.

4. **Create a .claudeignore.** Add your build artifacts and generated files.

---

← [Previous: Why AI Fails](01-why-ai-fails.md) | [🗺️ Course Map](../00-course-map.md) | [Next: AGENTS.md →](03-agents-md.md)
