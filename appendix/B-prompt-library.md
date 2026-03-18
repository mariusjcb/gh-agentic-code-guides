# 💬 Prompt Library — Copy-Paste Ready

> Your arsenal of battle-tested prompts. Replace `[PLACEHOLDERS]` with your context.

---

## 🔍 Exploration Prompts

### 1. Architecture Discovery
```
Explore the architecture of [MODULE_PATH]. I need to understand:
1. The overall pattern (MVVM, MVC, VIPER, etc.)
2. How data flows from UI → ViewModel → Repository → API
3. Key protocols/interfaces and their implementations
4. Dependency injection setup
5. Navigation patterns

Summarize as a brief architecture doc with a dependency diagram.
```

### 2. Pattern Identification
```
Scan [DIRECTORY] and identify the recurring coding patterns:
- How are ViewModels structured? (lifecycle, state management)
- How is error handling done? (Result types, throws, Combine/Flow)
- How are network calls made? (async/await, Combine, callbacks)
- How are tests written? (mocking strategy, assertion style)

Show me 2-3 representative examples of each pattern.
```

### 3. Dependency Mapping
```
Map all dependencies for [CLASS_NAME] in [FILE_PATH]:
- What does it depend on? (direct + transitive)
- What depends on it? (reverse dependencies)
- Which modules would break if this class changes?
- Draw a Mermaid dependency graph.
```

### 4. Quality Audit
```
Audit [MODULE_PATH] for common issues:
- Memory leaks (retain cycles, missing [weak self], CoroutineScope leaks)
- Thread safety (@MainActor missing, background UI updates)
- Error handling gaps (unhandled cases, swallowed errors)
- Missing accessibility labels
- Hardcoded strings/values

Rank findings by severity (🔴 Critical, 🟡 Warning, 🟢 Info).
```

---

## 🏗️ Architecture Prompts

### 5. Feature Design
```
Design the architecture for [FEATURE_NAME] in TaskPulse.

Context: [BRIEF DESCRIPTION]

Requirements:
- [REQUIREMENT_1]
- [REQUIREMENT_2]
- [REQUIREMENT_3]

Follow our existing patterns:
- MVVM with [Combine/Flow] for reactive bindings
- Repository pattern for data access
- Protocol/Interface-based DI

Output:
1. Module structure (files and folders)
2. Key protocols/interfaces
3. Data flow diagram (Mermaid)
4. Dependencies on existing modules
```

### 6. Migration Planning
```
Plan the migration of [COMPONENT] from [OLD_PATTERN] to [NEW_PATTERN].

Current state: [DESCRIBE CURRENT IMPLEMENTATION]
Target state: [DESCRIBE DESIRED IMPLEMENTATION]

I need:
1. Step-by-step migration plan (incremental, not big-bang)
2. Risk assessment for each step
3. Rollback strategy if something breaks
4. Estimated scope per step (files affected)
5. Test strategy to verify each step
```

### 7. SDK Evaluation
```
Evaluate [SDK_1] vs [SDK_2] vs [SDK_3] for [USE_CASE] in our iOS/Android project.

Criteria:
- API ergonomics (Swift/Kotlin idiomatic?)
- Bundle size impact
- Maintenance activity (last release, open issues)
- Community adoption
- Platform compatibility (min iOS/Android version)
- Privacy/security implications

Output as a decision matrix table with scores 1-5.
```

### 8. Architecture Review
```
Review the architecture of [MODULE_PATH] against these principles:
- Single Responsibility: Does each class have one job?
- Dependency Inversion: Are we depending on abstractions?
- Separation of Concerns: Is UI logic separate from business logic?
- Testability: Can each component be unit tested in isolation?

Flag violations with specific file:line references and suggested fixes.
```

---

## 💻 Implementation Prompts

### 9. SwiftUI Component
```
Create a SwiftUI view for [COMPONENT_NAME] in TaskPulse.

Following our patterns in Sources/Features/[EXISTING_FEATURE]/:
- ViewModel with @Published properties
- View with proper previews
- Match our design system (TaskPulseUI module)

Specs:
- [UI_REQUIREMENT_1]
- [UI_REQUIREMENT_2]
- Accessibility: VoiceOver labels for all interactive elements
- Support Dynamic Type

Include: View, ViewModel, Preview, and unit test file.
```

### 10. Compose Screen
```
Create a Compose screen for [SCREEN_NAME] in TaskPulse.

Following our patterns in features/[EXISTING_FEATURE]/:
- ViewModel with StateFlow<UiState>
- Compose screen with @Preview
- Use our design system tokens (Theme.colors, Theme.typography)

Specs:
- [UI_REQUIREMENT_1]
- [UI_REQUIREMENT_2]
- Accessibility: contentDescription for all interactive elements
- Handle configuration changes

Include: Screen composable, ViewModel, UiState, Preview, and test file.
```

### 11. API Integration
```
Implement the API integration for [ENDPOINT_NAME].

API spec:
- Method: [GET/POST/PUT/DELETE]
- URL: [BASE_URL]/[PATH]
- Request body: [JSON_STRUCTURE]
- Response: [JSON_STRUCTURE]

Following our networking patterns:
- Use our existing NetworkService/ApiClient
- Create DTO models with Codable/kotlinx.serialization
- Map to domain models
- Handle errors using our ErrorHandler
- Add to Repository layer

Include unit tests with mock responses.
```

### 12. Test Generation
```
Generate tests for [CLASS_NAME] at [FILE_PATH].

Match our testing style in [EXISTING_TEST_FILE]:
- Testing framework: [XCTest/JUnit/Kotest]
- Mocking: [Protocol mocks/Mockk/Mockito]
- Naming: test_[methodName]_[scenario]_[expectedResult]

Cover:
- Happy path for each public method
- Error cases and edge cases
- Boundary conditions
- Async behavior (if applicable)

Aim for >80% code coverage on business logic.
```

---

## 🐛 Debugging Prompts

### 13. Crash Investigation
```
Investigate this crash:

[PASTE CRASH LOG / STACK TRACE HERE]

I need:
1. Which line/function caused the crash
2. Likely root cause (top 3 hypotheses)
3. What state could trigger this
4. How to reproduce
5. Suggested fix with minimal blast radius
```

### 14. Memory Leak Hunt
```
Analyze [MODULE_PATH] for memory leaks:

1. Find all closures capturing self — are they using [weak self]?
2. Find delegate properties — are they weak references?
3. Find Combine/Flow subscriptions — are they properly cancelled?
4. Find Timer/Observer registrations — are they cleaned up in deinit/onCleared?
5. Check for circular references between ViewModels and Services

For each leak found, show the fix.
```

### 15. Performance Investigation
```
Analyze [SCREEN_NAME] for performance issues:

Symptoms: [DESCRIBE LAG, SLOW LOADING, JANK, ETC.]

Check for:
- Main thread blocking (heavy computation, synchronous I/O)
- Excessive recomposition/re-rendering
- Missing pagination (loading all data at once)
- Image loading without caching
- Unnecessary state updates triggering re-renders

Suggest fixes ranked by impact.
```

### 16. ANR/Hang Diagnosis
```
Diagnose this ANR/hang report:

[PASTE ANR TRACE OR THREAD DUMP]

1. Which thread is blocked and why?
2. What operation is taking too long?
3. Is there a deadlock?
4. What's the minimal fix to unblock the main thread?
```

---

## 📋 Planning Prompts

### 17. PRD Generation
```
I have an idea for a feature: [DESCRIBE YOUR IDEA IN 2-3 SENTENCES]

Create a PRD with:
- Problem Statement (what user pain does this solve?)
- User Stories (as a [role], I want [action], so that [benefit])
- Technical Requirements (specific, measurable, testable)
- Success Criteria (how do we know it works?)
- Constraints (platform limits, timeline, dependencies)
- Non-Goals (what this feature does NOT do)
- Risks & Mitigations

Format for AI consumption: clear, unambiguous, implementable.
```

### 18. RFC Creation
```
Create an RFC for: [FEATURE/TECHNICAL DECISION]

Context: [WHY THIS RFC IS NEEDED]

Include:
- Summary (1 paragraph)
- Motivation (the problem)
- Proposed Solution (the approach)
- Alternatives Considered (what else we evaluated)
- Technical Design (architecture, data models, APIs)
- Migration Strategy (if changing existing code)
- Testing Strategy
- Rollout Plan
- Open Questions
```

### 19. Sprint Ticket Breakdown
```
Break down this RFC into sprint-ready tickets:

[PASTE RFC SUMMARY OR LINK TO RFC FILE]

For each ticket:
- Title (clear, action-oriented)
- Description (acceptance criteria, technical notes)
- Estimate (S/M/L)
- Dependencies (which tickets must be done first)
- Labels (frontend, backend, testing, infra)

Order tickets by dependency — what must be built first?
```

### 20. FMEA Risk Analysis
```
Perform FMEA for [FEATURE/CHANGE]:

For each potential failure mode:
| Failure Mode | Severity (1-10) | Occurrence (1-10) | Detection (1-10) | RPN | Mitigation |

Consider:
- Data loss scenarios
- Security vulnerabilities
- UX degradation
- Performance regression
- Platform-specific failures (iOS/Android differences)
- Offline/poor connectivity failures
- Edge cases with existing features
```

---

## 🔄 Maintenance Prompts

### 21. Documentation Update
```
Update the documentation for [MODULE_NAME]:

Current docs: [PATH_TO_DOCS]
Current code: [PATH_TO_CODE]

1. Identify what's outdated (API changes, removed features, new patterns)
2. Update to match current implementation
3. Add missing documentation for new public APIs
4. Include code examples that actually compile
5. Update architecture diagrams if structure changed
```

### 22. Dependency Upgrade
```
Upgrade [DEPENDENCY_NAME] from [OLD_VERSION] to [NEW_VERSION].

1. Check changelog for breaking changes
2. Identify all usage points in our codebase
3. Update API calls that changed
4. Run tests to verify nothing broke
5. Update documentation if API surface changed

Use minimal changes — don't refactor unrelated code.
```

### 23. Dead Code Cleanup
```
Find dead code in [MODULE_PATH]:

1. Unused classes/structs/protocols/interfaces
2. Unused functions/methods
3. Unused imports
4. Commented-out code blocks
5. Feature-flagged code where flag was removed
6. Unreachable code paths

For each finding, verify it's truly unused (check tests too).
List files and line numbers for removal.
```

### 24. Refactoring Assist
```
Refactor [CLASS_NAME] to improve [SPECIFIC_ISSUE]:

Issue: [DESCRIBE THE PROBLEM — too large, too many responsibilities, hard to test, etc.]

Constraints:
- Don't change public API (other code depends on this)
- Keep all existing tests passing
- Extract in small, reviewable steps
- Each step should compile and pass tests

Show me the step-by-step refactoring plan, then implement step 1.
```

---

## 💡 Pro Tips

| Tip | Why |
|-----|-----|
| Start prompts with context | AI needs to know WHERE in the project you are |
| Reference existing files | "Match the style of X" beats describing the style |
| Set explicit constraints | "Don't modify files outside of Features/Chat/" |
| Ask for tests | Always include "Include unit tests" |
| Request explanations | "Explain your key decisions" helps you review |

---

[← A: Cheat Sheet](A-cheat-sheet.md) | [🗺️ Course Map](../00-course-map.md) | [C: Resources →](C-resources.md)
