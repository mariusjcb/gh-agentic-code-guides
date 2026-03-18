# 🤖 AGENTS.md — The Universal Config

> **One config file. Every AI tool. Zero vendor lock-in.**

What if your team uses Gemini CLI, but your contractor uses Cursor, and your CI runs GitHub Copilot? You'd need three config files — unless you use AGENTS.md.

---

## 🌐 What is AGENTS.md?

AGENTS.md is an **open standard** from the Linux Foundation that any AI coding tool can read.

```mermaid
flowchart TD
    A[AGENTS.md] --> B[Gemini CLI]
    A --> C[GitHub Copilot]
    A --> D[Cursor]
    A --> E[OpenAI Codex]
    A --> F[Future Tools]

    style A fill:#fef3c7,stroke:#d97706
```

**The numbers:**
- 📊 Adopted by **60,000+ repositories** since launch
- 🏢 Backed by the **Linux Foundation**
- 🔧 Supported by Gemini, Copilot, Cursor, Codex, and growing

**The idea is simple:** put your project rules in a format every tool understands. No more duplicating instructions across `.cursorrules`, `GEMINI.md`, `.github/copilot-instructions.md`, and `codex.md`.

---

## 🆚 GEMINI.md vs AGENTS.md

They're **complementary, not competing.**

| Feature | GEMINI.md | AGENTS.md |
|---------|-----------|-----------|
| **Scope** | Gemini CLI only | Any AI tool |
| **@imports** | ✅ Split into files | ❌ Single file |
| **Custom agents** | ✅ `/agents/` dir | ❌ Not supported |
| **Hooks (pre/post)** | ✅ Full hook system | ❌ Not supported |
| **MCP server config** | ✅ Via settings | ❌ Not supported |
| **Nested overrides** | ✅ Per-directory | ✅ Per-directory |
| **Tool adoption** | Gemini CLI | 60K+ repos, multi-tool |
| **Spec governance** | Google / Google DeepMind | Linux Foundation |

### When to use which?

```mermaid
flowchart TD
    Q{Does your team use<br/>only Gemini CLI?}
    Q -->|Yes| A[GEMINI.md is enough]
    Q -->|No| B{Multiple AI tools?}
    B -->|Yes| C[AGENTS.md for shared rules<br/>+ GEMINI.md for Gemini features]
    B -->|Just exploring| D[Start with AGENTS.md<br/>for portability]

    style A fill:#d1fae5
    style C fill:#dbeafe
    style D fill:#ede9fe
```

---

## 🤝 Using Both Together

The power move: **AGENTS.md for universal rules, GEMINI.md for Gemini-specific features.**

```
TaskPulse/
├── AGENTS.md         # Rules every AI tool reads
├── GEMINI.md         # Gemini-specific: hooks, imports, custom agents
├── .gemini/
│   ├── agents/       # Gemini custom agents (Part 2)
│   └── settings.json # MCP servers, permissions
```

**AGENTS.md** handles:
- Architecture rules
- Code style conventions
- Build commands
- Testing patterns
- File structure expectations

**GEMINI.md** adds:
- `@imports` for modular config
- Hook triggers (lint on save, test on commit)
- Custom agent definitions
- MCP server connections
- Gemini-specific behavioral tuning

### How they merge

When Gemini CLI sees both files, it reads **AGENTS.md first, then GEMINI.md.** GEMINI.md rules take priority on conflicts. Think of it as:

```
Final context = AGENTS.md (base) + GEMINI.md (override)
```

---

## 📱 Mobile Example — Cross-Platform Team

Here's a real scenario: your team has iOS devs using Gemini CLI, Android devs using Cursor, and a shared KMM module.

**AGENTS.md covers the shared ground:**

```markdown
# AGENTS.md — TaskPulse Cross-Platform Rules

## Architecture
- MVVM with Repository pattern across all platforms
- Unidirectional data flow: View → ViewModel → Repository → DataSource
- Shared business logic lives in :shared KMM module

## Shared Module (Kotlin Multiplatform)
- Location: shared/src/commonMain/
- All DTOs defined here, platform modules must not redefine them
- Use kotlinx.serialization for all JSON parsing
- Use kotlinx.coroutines Flow for reactive streams

## API Contract
- Base URL configured per environment in BuildConfig / Info.plist
- All endpoints return ApiResponse<T> wrapper
- Error codes: see shared/src/commonMain/model/ErrorCode.kt
- Auth: Bearer token in Authorization header, refresh via /auth/refresh

## Code Review Standards
- No platform-specific code in :shared module
- All public APIs require KDoc/DocC comments
- Max file length: 200 lines (split if larger)
- All ViewModels must handle: loading, success, error, empty states
```

Then each platform adds its own tool-specific config:

**iOS team's GEMINI.md:**
```markdown
# TaskPulse iOS — Gemini Config
@.gemini/swift-style.md
@.gemini/testing-ios.md

## Hooks
- Pre-commit: swiftlint --strict
- Post-generate: swift build --package-path Packages/Core
```

**Android team's `.cursorrules`:**
```
Follow AGENTS.md for architecture.
Use ktlint for formatting.
Run ./gradlew :app:testDebugUnitTest after changes.
```

**Same rules, different tools, zero drift.**

---

## 📝 TaskPulse AGENTS.md — Complete Example

```markdown
# AGENTS.md — TaskPulse

## Project
TaskPulse is a collaborative task manager with real-time sync and offline support.
Two native clients: iOS (SwiftUI) and Android (Jetpack Compose).

## Architecture
- Pattern: MVVM with Repository pattern
- Data flow: View → ViewModel → Repository → LocalDataSource + RemoteDataSource
- Navigation: Coordinator pattern (iOS), Navigation Component (Android)
- DI: Protocol-based (iOS), Interface-based with Hilt (Android)

## Module Structure
```
iOS:  Core → Networking → Features/{FeatureName} → App
Android: :core → :networking → :features:{feature-name} → :app
```
UI components are shared. Never duplicate across feature modules.

## Code Conventions
- ViewModels manage UI state as a single sealed type/enum
- All async work scoped to ViewModel lifecycle
- No business logic in Views/Composables
- Error handling: map all errors through ErrorMapper before UI
- File limit: 200 lines max. Split into extensions/partials if needed.

## State Management
- iOS: @Published properties, @MainActor ViewModels
- Android: StateFlow, viewModelScope coroutines
- Both: UiState pattern with Loading / Success / Error / Empty variants

## Testing Requirements
- Every ViewModel: test loading, success, error, empty states
- Use fakes/mocks from shared test utilities (TestHelpers/ or :core:testing)
- No live network calls in unit tests
- Snapshot tests for all shared UI components

## Build Commands
iOS:
  xcodebuild -scheme TaskPulse -destination 'platform=iOS Simulator,name=iPhone 16,OS=18.0' test

Android:
  ./gradlew :app:assembleDebug
  ./gradlew :app:testDebugUnitTest --continue

## Don't
- Don't use GlobalScope (Android) or detached Tasks without cancellation (iOS)
- Don't force-unwrap optionals or use lateinit for UI state
- Don't put UI framework imports in ViewModel layer
- Don't add third-party dependencies without documenting the reason
- Don't store secrets in source code — use environment config
```

---

## ⚡ Quick Decision Guide

Starting fresh? Use this:

| Situation | Use |
|-----------|-----|
| Solo dev, Gemini CLI only | `GEMINI.md` |
| Team, all Gemini CLI | `GEMINI.md` |
| Team, mixed AI tools | `AGENTS.md` + `GEMINI.md` |
| Open-source project | `AGENTS.md` (widest compatibility) |
| Enterprise, multi-team | `AGENTS.md` (base) + tool-specific overrides |

---

## 🧪 Try It Now

1. **Create an AGENTS.md** for your project with these sections:
   - [ ] Project description (2–3 sentences)
   - [ ] Architecture pattern
   - [ ] Module structure
   - [ ] 5 code conventions
   - [ ] Build/test commands

2. **Compare coverage.** If you already have a GEMINI.md, identify which rules are universal (move to AGENTS.md) vs Gemini-specific (keep in GEMINI.md).

3. **Test portability.** Copy your AGENTS.md into a Cursor project or Copilot workspace. Does the AI follow your rules? Note any gaps.

4. **Check the ecosystem.** Browse [github.com/topics/agents-md](https://github.com/topics/agents-md) to see how other projects structure theirs.

---

← [Previous: GEMINI.md Basics](02-gemini-md-basics.md) | [🗺️ Course Map](../00-course-map.md) | [Next: Smart Prompting →](04-smart-prompting.md)
