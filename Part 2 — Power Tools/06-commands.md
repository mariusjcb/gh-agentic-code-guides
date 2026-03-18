# 🔧 Custom Slash Commands

> **Time to read:** ~5 min | **Skill level:** Intermediate | **Platform:** iOS & Android

Custom agents are specialized subagents. Commands are **on-demand power tools.** Type `/build-ios` and Gemini runs your exact build pipeline. No remembering flags. No typos. No "wait, which simulator was it again?"

---

## 🎯 What's a Command?

A **command** is a user-triggered `/slash-command`. You invoke it explicitly by typing `/command-name` in your Gemini session. Commands are defined in `.toml` files and managed via the `/commands` interface.

```
You:  /build-ios
Gemini: Running xcodebuild for TaskPulse... ✅ Build succeeded.
```

Commands are perfect for **actions you repeat daily** but don't want happening automatically.

---

## 📁 File Structure

Commands are defined in TOML configuration files within `.gemini/`:

```
.gemini/
  commands/
    build-ios.toml
    build-android.toml
    test-module.toml
    lint-check.toml
    review-pr.toml
    generate-mocks.toml
```

Each command = one `.toml` file in `.gemini/commands/`. The filename becomes the slash command name.

`build-ios.toml` → `/build-ios`

### TOML Command Format

Each `.toml` file defines the command with a description and prompt:

```toml
[command]
description = "Short description shown in /commands list"
prompt = """
The full prompt/instructions that Gemini will follow
when this command is invoked.
"""
```

Use `/commands` in a Gemini session to list all available custom commands.

---

## 🆚 Custom Agents vs Commands

| | ⚡ Custom Agents | 🔧 Commands |
|---|---|---|
| **Trigger** | Automatic (context-based) | Manual (`/command-name`) |
| **Location** | `.gemini/agents/{name}.md` | `.gemini/commands/{name}.toml` |
| **Format** | Markdown with YAML frontmatter | TOML with description + prompt |
| **Best for** | Code generation patterns | Build/test/lint actions |
| **Parameters** | None | Arguments appended to prompt |
| **Think of it as** | "Always remember this" | "Do this when I say so" |

**Rule of thumb:**
- Generating code? → **Custom Agent**
- Running a task? → **Command**

---

## 📱 6 Mobile Commands

### 1️⃣ `/build-ios`

**`.gemini/commands/build-ios.toml`**

```toml
[command]
description = "Build the TaskPulse iOS app for the simulator"
prompt = """
Build the TaskPulse iOS app for the simulator.

Run this exact command:

```bash
xcodebuild build \
  -workspace TaskPulse.xcworkspace \
  -scheme TaskPulse \
  -destination 'platform=iOS Simulator,name=iPhone 16,OS=latest' \
  -configuration Debug \
  CODE_SIGNING_ALLOWED=NO \
  | xcpretty
```

If the build fails:
1. Show the FIRST error only (not the cascade)
2. Identify the root cause
3. Suggest a fix
4. Ask before applying changes

Never change build settings without asking.
"""
```

---

### 2️⃣ `/build-android`

**`.gemini/commands/build-android.toml`**

```toml
[command]
description = "Build the TaskPulse Android app"
prompt = """
Build the TaskPulse Android app.

Run this exact command:

```bash
./gradlew :app:assembleDebug --warning-mode all 2>&1
```

If the build fails:
1. Show the FIRST error (skip the Gradle noise)
2. Check if it's a dependency issue, compilation error, or config problem
3. Suggest a targeted fix
4. Ask before modifying any gradle files

For release builds, use:
```bash
./gradlew :app:assembleRelease
```
"""
```

---

### 3️⃣ `/test-module`

**`.gemini/commands/test-module.toml`**

```toml
[command]
description = "Run tests for a specific module"
prompt = """
Run tests for a specific module.

## Usage
`/test-module TaskList` or `/test-module feature:chat`

The user will provide the module name as an argument after the command.

## iOS
```bash
xcodebuild test \
  -workspace TaskPulse.xcworkspace \
  -scheme TaskPulse \
  -destination 'platform=iOS Simulator,name=iPhone 16,OS=latest' \
  -only-testing:{module_argument} \
  | xcpretty
```

## Android
```bash
./gradlew :feature:{module_argument}:testDebugUnitTest
```

## After running:
- Report total tests, passed, failed, skipped
- For failures: show test name, expected vs actual, file location
- Suggest fixes for failing tests
- Do NOT auto-fix tests without asking
"""
```

---

### 4️⃣ `/lint-check`

**`.gemini/commands/lint-check.toml`**

```toml
[command]
description = "Run all linters for TaskPulse"
prompt = """
Run all linters for TaskPulse.

## iOS — SwiftLint
```bash
swiftlint lint --config .swiftlint.yml --reporter emoji 2>&1
```

## Android — Detekt
```bash
./gradlew detekt 2>&1
```

## Report format:
Summarize results as:

| Platform | Warnings | Errors | Auto-fixable |
|----------|----------|--------|-------------|
| iOS | X | X | X |
| Android | X | X | X |

If there are auto-fixable issues, ask if I want to fix them:
- iOS: `swiftlint lint --fix`
- Android: `./gradlew detekt --auto-correct`
"""
```

---

### 5️⃣ `/review-pr`

**`.gemini/commands/review-pr.toml`**

```toml
[command]
description = "Review the current PR for mobile-specific issues"
prompt = """
Review the current PR for mobile-specific issues.

Check the diff and look for these categories:

## 🔴 Critical
- **Retain cycles**: Missing `[weak self]` in closures (iOS)
- **Thread safety**: UI updates off main thread, missing `@MainActor`
- **CoroutineScope leaks**: `GlobalScope` usage, missing cancellation (Android)
- **Force unwraps**: `!` usage without justification (iOS)

## 🟡 Important
- **Accessibility**: Missing labels, broken VoiceOver/TalkBack order
- **Memory**: Large allocations in `body` (SwiftUI) or Composable functions
- **Error handling**: Swallowed errors, missing user feedback
- **Combine/Flow**: Subscriptions not cancelled, missing `.receive(on:)`

## 🟢 Suggestions
- Naming conventions (TaskPulse style)
- Missing previews/tests
- Documentation gaps

## Output format:
Group findings by severity. For each issue:
- 📍 File + line number
- 🐛 What's wrong
- ✅ How to fix (with code snippet)
"""
```

---

### 6️⃣ `/generate-mocks`

**`.gemini/commands/generate-mocks.toml`**

```toml
[command]
description = "Generate mock implementations for testing"
prompt = """
Generate mock implementations for testing.

Usage: `/generate-mocks TaskRepository`

The user will provide the protocol/interface name as an argument.

## iOS
Find the protocol provided by the user and create a mock:

```swift
final class Mock{Name}: {Name} {
    // Track call counts
    var {method}CallCount = 0
    // Stubbed returns
    var stubbed{Method}Result: {ReturnType}!

    func {method}(...) async throws -> {ReturnType} {
        {method}CallCount += 1
        return stubbed{Method}Result
    }
}
```

Place in `Tests/Mocks/Mock{Name}.swift`

## Android
Find the interface provided by the user and create a mock using MockK pattern:

```kotlin
// If simple, suggest mockk<{Name}>() inline
// If complex, create a Fake:
class Fake{Name} : {Name} {
    var {method}Result: Result<{ReturnType}> = Result.success(/* default */)

    override suspend fun {method}(...): {ReturnType} {
        return {method}Result.getOrThrow()
    }
}
```

Place in `feature/{module}/src/test/fakes/Fake{Name}.kt`
"""
```

---

## 🔧 Passing Arguments to Commands

Commands accept parameters as additional text after the command name. The argument text is appended to the prompt that Gemini receives:

```
You:  /test-module TaskList
                   ^^^^^^^^
                   This is passed as context to the command prompt
```

```
You:  /generate-mocks TaskRepository
                      ^^^^^^^^^^^^^^
                      Gemini receives this alongside the command prompt
```

Reference the expected argument in your prompt so Gemini knows how to use it.

---

## 📋 Organizing Commands

### Naming Conventions

| Prefix | Purpose | Examples |
|--------|---------|---------|
| `build-` | Compilation | `build-ios`, `build-android` |
| `test-` | Testing | `test-module`, `test-snapshot` |
| `lint-` | Code quality | `lint-check`, `lint-fix` |
| `gen-` | Code generation | `generate-mocks`, `gen-model` |
| `review-` | Analysis | `review-pr`, `review-perf` |
| `deploy-` | Release | `deploy-testflight`, `deploy-beta` |

### Team Setup

```
.gemini/commands/
  build-ios.toml        # 🏗️ Build
  build-android.toml
  test-module.toml      # 🧪 Test
  test-snapshot.toml
  lint-check.toml       # 🔍 Quality
  lint-fix.toml
  review-pr.toml        # 📋 Review
  generate-mocks.toml   # ⚙️ Generate
```

**Commit these to your repo.** Every team member gets the same power tools. Use `/commands` to see all available commands at a glance.

---

## 🧪 Try It Now

1. **Create `/build-ios` or `/build-android`:** Adapt the TOML template above to your actual project's build command. Test it.

2. **Create a parameterized command:** Build `/test-module` that accepts a module name as an argument. Test it with different module names.

3. **Create `/review-pr`:** Customize the checklist for your team's most common code review issues. Run it on your last PR.

4. **Speed test:** Time yourself running your build + lint + test flow manually vs. using 3 slash commands. Feel the difference.

5. **Share with your team:** Commit your `.gemini/commands/` directory and watch teammates discover the commands via `/commands`.

---

← Previous: [05-skills.md](05-skills.md) | [🗺️ Course Map](../00-course-map.md) | Next →: [07-hooks.md](07-hooks.md)
