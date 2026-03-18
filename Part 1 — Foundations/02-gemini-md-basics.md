# 📝 Your First GEMINI.md

> **One file. Infinite context. Every session.**

`GEMINI.md` is a markdown file at your project root that Gemini CLI reads **automatically** at the start of every session. It's your AI's onboarding doc, style guide, and architecture reference — combined.

No more repeating yourself. No more "actually, we use MVVM here."

---

## 🚀 Quick Start

**30 seconds to your first GEMINI.md:**

```bash
# In your project root
gemini

# Then type:
/memory add "This is a SwiftUI + MVVM project called TaskPulse"
```

Gemini CLI stores memory entries that persist across sessions. You can also create a `GEMINI.md` file directly in your project root for structured project context. It'll detect:
- 📦 Package manager (SPM, CocoaPods, Gradle)
- 🏗️ Project structure and modules
- 🧪 Test frameworks and patterns
- 🔧 Build commands

**Review it immediately.** The auto-generated version is a starting point — you'll customize it heavily.

---

## 📐 Anatomy of GEMINI.md

Every good `GEMINI.md` has these sections. Here's what each looks like for TaskPulse:

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

> Frontier LLMs reliably follow **150–200 instructions.** Gemini's own system prompt uses ~50 of those slots. That leaves **~100–150 effective instructions** for your GEMINI.md.

| Budget | Used By |
|--------|---------|
| ~200 total | LLM instruction-following capacity |
| ~50 | Gemini's system prompt |
| **~150** | **Your GEMINI.md budget** |

**Practical limit: ~300 lines of markdown** (comments, headers, and whitespace don't count as instructions).

Going over? Your later rules get ignored. The fix: **@file.md imports.**

---

## 🔗 @file.md Imports — Splitting for Sanity

Break your GEMINI.md into focused files using the `@file.md` import syntax. Only the relevant content loads per session.

```markdown
# In your root GEMINI.md:
@.gemini/architecture.md
@.gemini/testing-conventions.md
@.gemini/api-patterns.md
```

### TaskPulse Import Structure

```
.gemini/
├── architecture.md        # MVVM layers, module boundaries, dependency graph
├── testing-conventions.md # XCTest/JUnit patterns, mock generation rules
├── api-patterns.md        # NetworkService protocol, endpoint conventions
├── swift-style.md         # iOS-specific code style
├── kotlin-style.md        # Android-specific code style
└── review-checklist.md    # PR review criteria
```

**`.gemini/architecture.md` example:**

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

**`.gemini/testing-conventions.md` example:**

```markdown
## Testing Rules
- Every ViewModel gets a corresponding test file
- Use MockTaskService (already exists in TestHelpers/)
- iOS: XCTest + Swift Testing. No third-party test frameworks.
- Android: JUnit5 + Turbine for Flow testing + MockK for mocks
- Minimum: test loading, success, and error states for every screen
```

---

## 🧠 /memory Commands

Gemini CLI provides built-in `/memory` commands for managing persistent context without editing files directly:

```bash
# Add a memory entry
/memory add "Always use async/await instead of Combine for new Swift code"

# List all memory entries
/memory list

# Show full memory contents
/memory show

# Reload memory from disk (after manual edits)
/memory reload
```

Memory entries are stored in `GEMINI.md` files and persist across sessions. Use `/memory add` for quick one-liners and edit `GEMINI.md` directly for structured, multi-line project context.

---

## 🚫 .geminiignore

Keep AI focused. Exclude noise.

```gitignore
# .geminiignore
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

## 📂 Hierarchical GEMINI.md Loading

Gemini CLI loads `GEMINI.md` files hierarchically: **global → project root → subdirectory**. Subdirectory-level files **extend and override** higher-level rules for that directory.

```
~/.gemini/
├── GEMINI.md                             # Global — rules for ALL projects

TaskPulse/
├── GEMINI.md                             # Project root — project-wide rules
├── Sources/
│   ├── Features/
│   │   ├── Chat/
│   │   │   └── GEMINI.md                # Chat-specific rules
│   │   └── TaskList/
│   │       └── GEMINI.md                # TaskList-specific rules
│   └── Networking/
│       └── GEMINI.md                     # API layer rules
```

**Loading order:**
1. `~/.gemini/GEMINI.md` — Global defaults (your personal style preferences)
2. `TaskPulse/GEMINI.md` — Project-wide rules
3. `TaskPulse/Sources/Features/Chat/GEMINI.md` — Directory-specific overrides

**`Features/Chat/GEMINI.md` example:**

```markdown
# Chat Module Rules
- All messages must go through MessageEncryptionService before sending
- Use WebSocketManager (not raw URLSession) for real-time updates
- Message timestamps: always UTC, convert to local only in View layer
- Reference implementation: MessageBubbleView.swift for UI patterns
```

This is powerful. The Chat module has security rules that don't apply elsewhere. Hierarchical GEMINI.md keeps them scoped.

---

## 📋 Complete TaskPulse GEMINI.md

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

@.gemini/api-patterns.md
@.gemini/testing-conventions.md
```

**~80 lines. Under budget. Covers both platforms.**

---

## 🧪 Try It Now

1. **Create your GEMINI.md.** Create a `GEMINI.md` in your project root, then customize:
   - [ ] Add your project's architecture pattern
   - [ ] Add 3 code style rules AI keeps violating
   - [ ] Add your exact build/test commands
   - [ ] Add one "Don't" rule based on past AI mistakes

2. **Test it.** Start a new Gemini CLI session and ask: *"What architecture pattern does this project use?"* It should answer from your GEMINI.md without looking at code.

3. **Extract one @file.md import.** Move your testing conventions to `.gemini/testing-conventions.md` and add the `@` import to your root GEMINI.md.

4. **Create a .geminiignore.** Add your build artifacts and generated files.

5. **Try /memory commands.** Run `/memory add` to store a quick rule, then `/memory list` to verify it persisted.

---

← [Previous: Why AI Fails](01-why-ai-fails.md) | [🗺️ Course Map](../00-course-map.md) | [Next: AGENTS.md →](03-agents-md.md)
