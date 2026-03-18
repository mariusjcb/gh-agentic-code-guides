# ⚡ Creating Custom Agents

> **Time to read:** ~5 min | **Skill level:** Intermediate | **Platform:** iOS & Android

You've been explaining the same patterns to Gemini over and over. Your team's SwiftUI component structure. Your Compose screen conventions. Your API layer patterns. **Stop repeating yourself.**

Custom agents are reusable, specialized subagents that Gemini loads when you need domain-specific expertise. Write once, benefit forever.

---

## 🎯 What's a Custom Agent?

A **Custom Agent** is a markdown file with YAML frontmatter that defines a specialized subagent for Gemini CLI.

| | Custom Agents | Manual Instructions |
|---|---|---|
| **Trigger** | 🤖 Invoked by Gemini when relevant | 👤 You type it every time |
| **Location** | `.gemini/agents/` (project) or `~/.gemini/agents/` (user) | Your brain / clipboard |
| **Consistency** | Always the same | Varies by your mood & memory |
| **Team sharing** | Git-committed, everyone gets it | "Check the wiki maybe?" |

**Think of custom agents as specialist teammates your AI pair programmer can call on.**

---

## 📁 File Structure

```
.gemini/
  agents/
    swift-ui-component.md
    compose-screen.md
    api-integration.md
    unit-test-writer.md
```

Each custom agent is a `.md` file in `.gemini/agents/`. The filename identifies the agent.

**Important:** Custom agents must be enabled in your `.gemini/settings.json`:

```json
{
  "agents": {
    "swift-ui-component": { "enabled": true },
    "compose-screen": { "enabled": true },
    "api-integration": { "enabled": true },
    "unit-test-writer": { "enabled": true }
  }
}
```

You can also place agents in `~/.gemini/agents/` for user-level agents available across all your projects.

---

## 📝 YAML Frontmatter

Every custom agent starts with a YAML frontmatter block between `---` delimiters:

```yaml
---
name: "Human-readable name"
description: "Short description of what this agent specializes in"
---
```

- **`name`** — Display name for the agent
- **`description`** — Gemini reads this to understand the agent's specialty and decide when to delegate to it

**Keep descriptions specific.** "Helps with code" = useless. "Generates SwiftUI views following TaskPulse design system with previews" = chef's kiss.

---

## 🔄 How Activation Works

```mermaid
flowchart LR
    A["👤 User Prompt"] --> B{"🤖 Gemini checks\nagent descriptions"}
    B -->|Match found| C["📖 Agent instructions\nloaded"]
    B -->|No match| D["Standard response"]
    C --> E["✨ Better, consistent\noutput"]
```

1. You type a prompt like *"Create a new task detail screen"*
2. Gemini evaluates which custom agent is best suited for the task
3. `compose-screen` agent matches — its instructions are loaded
4. Gemini follows your team's exact patterns. No reminding needed.

---

## 📱 Mobile Agent Examples

### 1️⃣ `swift-ui-component` Agent

**`.gemini/agents/swift-ui-component.md`**

```markdown
---
name: "SwiftUI Component Generator"
description: "Generates SwiftUI view components following TaskPulse iOS design system with previews"
---

# SwiftUI Component Generator — TaskPulse

When creating any SwiftUI view for TaskPulse:

## Structure
- Place in `Sources/UI/Components/` (reusable) or `Sources/UI/Screens/` (full screens)
- Use MVVM: View observes a `@Observable` ViewModel
- Never put business logic in the View

## Naming
- Views: `{Feature}View.swift` (e.g., `TaskDetailView.swift`)
- ViewModels: `{Feature}ViewModel.swift`

## Required patterns
- Add `#Preview` macro with at least 2 states (loaded, empty)
- Use `TaskPulseTheme` tokens for colors/spacing — NEVER hardcode
- Support Dynamic Type via `.font(.tpBody)` etc.
- Include accessibility labels on all interactive elements
- Use `@MainActor` on ViewModels

## Template
```swift
import SwiftUI

struct {Name}View: View {
    @State private var viewModel: {Name}ViewModel

    var body: some View {
        // Content here
    }
}

#Preview("Loaded") {
    {Name}View(viewModel: .preview)
}

#Preview("Empty") {
    {Name}View(viewModel: .emptyPreview)
}
```
```

---

### 2️⃣ `compose-screen` Agent

**`.gemini/agents/compose-screen.md`**

```markdown
---
name: "Compose Screen Generator"
description: "Generates Jetpack Compose screens with ViewModel following TaskPulse Android patterns"
---

# Compose Screen Generator — TaskPulse

When creating any Compose screen for TaskPulse:

## Structure
- Place in `feature/{name}/ui/` for screens
- Place in `core/ui/components/` for shared components
- ViewModel in `feature/{name}/viewmodel/`

## Naming
- Screens: `{Feature}Screen.kt`
- ViewModels: `{Feature}ViewModel.kt`
- State: `{Feature}UiState.kt` (sealed interface)

## Required patterns
- Stateless composable: Screen receives state + callbacks
- ViewModel exposes `StateFlow<UiState>`
- Use `collectAsStateWithLifecycle()` — NOT `collectAsState()`
- Use `TaskPulseTheme` tokens — never hardcode colors/dimens
- Include `@Preview` with `@PreviewLightDark`

## Template
```kotlin
@Composable
fun {Name}Screen(
    viewModel: {Name}ViewModel = hiltViewModel()
) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    {Name}Content(
        state = uiState,
        onAction = viewModel::onAction
    )
}

@Composable
private fun {Name}Content(
    state: {Name}UiState,
    onAction: ({Name}Action) -> Unit
) {
    // UI here
}

@PreviewLightDark
@Composable
private fun {Name}Preview() {
    TaskPulseTheme {
        {Name}Content(
            state = {Name}UiState.Loaded(/* preview data */),
            onAction = {}
        )
    }
}
```
```

---

### 3️⃣ `api-integration` Agent

**`.gemini/agents/api-integration.md`**

```markdown
---
name: "API Integration Agent"
description: "Creates network layer API calls following TaskPulse patterns for both iOS and Android"
---

# API Integration — TaskPulse

## iOS (Swift)
- Use `TaskPulseAPIClient` (wraps URLSession)
- Define endpoints in `Sources/Networking/Endpoints/`
- Use `async throws` — never completion handlers
- Models in `Sources/Models/API/` with Codable
- Always use `TaskPulseError` for error mapping

```swift
// Endpoint definition
enum TaskEndpoint: APIEndpoint {
    case getTask(id: String)
    case updateTask(id: String, body: UpdateTaskRequest)

    var path: String { /* ... */ }
    var method: HTTPMethod { /* ... */ }
}
```

## Android (Kotlin)
- Use Retrofit interfaces in `core/network/api/`
- DTOs in `core/network/dto/` with `@Serializable`
- Map DTOs → domain models in repository layer
- Use `suspend` functions, handle with `Result<T>`

```kotlin
interface TaskApi {
    @GET("tasks/{id}")
    suspend fun getTask(@Path("id") id: String): TaskDto

    @PUT("tasks/{id}")
    suspend fun updateTask(
        @Path("id") id: String,
        @Body body: UpdateTaskRequest
    ): TaskDto
}
```
```

---

### 4️⃣ `unit-test-writer` Agent

**`.gemini/agents/unit-test-writer.md`**

```markdown
---
name: "Unit Test Writer"
description: "Generates unit tests matching TaskPulse conventions for XCTest (iOS) and JUnit (Android)"
---

# Unit Test Writer — TaskPulse

## iOS (XCTest)
- Place in `Tests/{Module}Tests/`
- Name: `{Class}Tests.swift`
- Use `// MARK: -` to group by method
- Pattern: Given / When / Then in comments
- Use `TaskPulseMocks` for dependencies

```swift
@MainActor
final class TaskViewModelTests: XCTestCase {
    private var sut: TaskViewModel!
    private var mockRepo: MockTaskRepository!

    override func setUp() {
        mockRepo = MockTaskRepository()
        sut = TaskViewModel(repository: mockRepo)
    }

    // MARK: - loadTask
    func test_loadTask_success_updatesState() async {
        // Given
        mockRepo.stubbedTask = .preview

        // When
        await sut.loadTask(id: "123")

        // Then
        XCTAssertEqual(sut.state, .loaded(.preview))
    }
}
```

## Android (JUnit + Turbine)
- Place in `feature/{name}/src/test/`
- Use Turbine for Flow testing
- Use MockK for mocking
- Pattern: Given / When / Then

```kotlin
class TaskViewModelTest {
    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private val mockRepo = mockk<TaskRepository>()
    private lateinit var viewModel: TaskViewModel

    @Test
    fun `loadTask success updates state`() = runTest {
        // Given
        coEvery { mockRepo.getTask("123") } returns Result.success(testTask)
        viewModel = TaskViewModel(mockRepo)

        // When / Then
        viewModel.uiState.test {
            viewModel.loadTask("123")
            assertThat(awaitItem()).isEqualTo(UiState.Loaded(testTask))
        }
    }
}
```
```

---

## 💡 The Rule of Three

> **If you've explained something to your AI 3 times, make it a custom agent.**

Signs you need a custom agent:

- 🔁 You keep pasting the same "remember to..." instructions
- 😤 Gemini forgets your team's patterns between sessions
- 📋 You have a "prompt template" saved somewhere
- 👥 New team members ask "how does Gemini know our conventions?"

**Turn tribal knowledge into committed custom agents.**

---

## 🆚 Custom Agents vs Manual Instructions

### Without custom agent:
```
Create a new SwiftUI view for the task detail screen.
Remember to use MVVM pattern.
Use our TaskPulseTheme tokens, not hardcoded colors.
Add preview macros with loaded and empty states.
Put it in Sources/UI/Screens/.
Make sure the ViewModel uses @MainActor.
Oh and add accessibility labels.
```

### With `swift-ui-component` agent:
```
Create a new SwiftUI view for the task detail screen.
```

**Same output. 80% less typing.** Gemini auto-loads everything it needs.

---

## 🧪 Try It Now

1. **Create your first custom agent:** Pick your most-repeated instruction pattern. Create a `.md` file in `.gemini/agents/` with proper YAML frontmatter.

2. **Enable it in settings:** Add the agent to your `.gemini/settings.json` with `"enabled": true`.

3. **Port a real pattern:** Take a component you recently built in TaskPulse. Write a custom agent that would generate it correctly from a one-line prompt.

4. **Team challenge:** Ask a teammate to create a component using your custom agent. Did the output match team standards without any extra prompting?

---

← Previous: [04-context-management.md](../Part%201%20—%20Foundations/04-context-management.md) | [🗺️ Course Map](../00-course-map.md) | Next →: [06-commands.md](06-commands.md)
