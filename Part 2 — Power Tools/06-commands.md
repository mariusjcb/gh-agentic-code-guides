# 🔧 Custom Slash Commands

> **Time to read:** ~5 min | **Skill level:** Intermediate | **Platform:** iOS & Android

Skills are automatic. Commands are **on-demand power tools.** Type `/build-ios` and Claude runs your exact build pipeline. No remembering flags. No typos. No "wait, which simulator was it again?"

---

## 🎯 What's a Command?

A **command** is a user-triggered `/slash-command`. You invoke it explicitly by typing `/command-name` in your Claude session.

```
You:  /build-ios
Claude: Running xcodebuild for TaskPulse... ✅ Build succeeded.
```

Commands are perfect for **actions you repeat daily** but don't want happening automatically.

---

## 📁 File Structure

```
.claude/
  commands/
    build-ios.md
    build-android.md
    test-module.md
    lint-check.md
    review-pr.md
    generate-mocks.md
```

Each command = one `.md` file in `.claude/commands/`. The filename becomes the slash command name.

`build-ios.md` → `/build-ios`

---

## 🆚 Skills vs Commands

| | ⚡ Skills | 🔧 Commands |
|---|---|---|
| **Trigger** | Automatic (context-based) | Manual (`/command-name`) |
| **Location** | `.claude/skills/{name}/SKILL.md` | `.claude/commands/{name}.md` |
| **Best for** | Code generation patterns | Build/test/lint actions |
| **Parameters** | None | `$ARGUMENTS` supported |
| **Think of it as** | "Always remember this" | "Do this when I say so" |

**Rule of thumb:**
- Generating code? → **Skill**
- Running a task? → **Command**

---

## 📱 6 Mobile Commands

### 1️⃣ `/build-ios`

**`.claude/commands/build-ios.md`**

```markdown
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
```

---

### 2️⃣ `/build-android`

**`.claude/commands/build-android.md`**

```markdown
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
```

---

### 3️⃣ `/test-module`

**`.claude/commands/test-module.md`**

```markdown
Run tests for a specific module.

## Usage
`/test-module TaskList` or `/test-module feature:chat`

## iOS
```bash
xcodebuild test \
  -workspace TaskPulse.xcworkspace \
  -scheme TaskPulse \
  -destination 'platform=iOS Simulator,name=iPhone 16,OS=latest' \
  -only-testing:$ARGUMENTS \
  | xcpretty
```

## Android
```bash
./gradlew :feature:$ARGUMENTS:testDebugUnitTest
```

## After running:
- Report total tests, passed, failed, skipped
- For failures: show test name, expected vs actual, file location
- Suggest fixes for failing tests
- Do NOT auto-fix tests without asking
```

---

### 4️⃣ `/lint-check`

**`.claude/commands/lint-check.md`**

```markdown
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
```

---

### 5️⃣ `/review-pr`

**`.claude/commands/review-pr.md`**

```markdown
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
```

---

### 6️⃣ `/generate-mocks`

**`.claude/commands/generate-mocks.md`**

```markdown
Generate mock implementations for testing.

Usage: `/generate-mocks TaskRepository` or `/generate-mocks $ARGUMENTS`

## iOS
Find the protocol `$ARGUMENTS` and create a mock:

```swift
final class Mock$ARGUMENTS: $ARGUMENTS {
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

Place in `Tests/Mocks/Mock$ARGUMENTS.swift`

## Android
Find the interface `$ARGUMENTS` and create a mock using MockK pattern:

```kotlin
// If simple, suggest mockk<$ARGUMENTS>() inline
// If complex, create a Fake:
class Fake$ARGUMENTS : $ARGUMENTS {
    var {method}Result: Result<{ReturnType}> = Result.success(/* default */)

    override suspend fun {method}(...): {ReturnType} {
        return {method}Result.getOrThrow()
    }
}
```

Place in `feature/{module}/src/test/fakes/Fake$ARGUMENTS.kt`
```

---

## 🔧 The `$ARGUMENTS` Variable

Commands can accept parameters. Use `$ARGUMENTS` as a placeholder:

```markdown
<!-- .claude/commands/open-file.md -->
Open the file at path $ARGUMENTS and explain its purpose and architecture.
```

```
You:  /open-file Sources/UI/Screens/TaskDetailView.swift
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                 This replaces $ARGUMENTS
```

You can use `$ARGUMENTS` multiple times in one command. It's always the full text after the command name.

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
.claude/commands/
  build-ios.md        # 🏗️ Build
  build-android.md
  test-module.md      # 🧪 Test
  test-snapshot.md
  lint-check.md       # 🔍 Quality
  lint-fix.md
  review-pr.md        # 📋 Review
  generate-mocks.md   # ⚙️ Generate
```

**Commit these to your repo.** Every team member gets the same power tools.

---

## 🧪 Try It Now

1. **Create `/build-ios` or `/build-android`:** Adapt the template above to your actual project's build command. Test it.

2. **Create a parameterized command:** Build `/test-module` with `$ARGUMENTS`. Test it with different module names.

3. **Create `/review-pr`:** Customize the checklist for your team's most common code review issues. Run it on your last PR.

4. **Speed test:** Time yourself running your build + lint + test flow manually vs. using 3 slash commands. Feel the difference.

5. **Share with your team:** Commit your `.claude/commands/` directory and watch teammates discover the commands.

---

← Previous: [05-skills.md](05-skills.md) | [🗺️ Course Map](../00-course-map.md) | Next →: [07-hooks.md](07-hooks.md)
