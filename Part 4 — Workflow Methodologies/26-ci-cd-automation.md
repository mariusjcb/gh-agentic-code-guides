# 🔧 CI/CD Automation — Claude in Your Pipeline

> **Time to read:** ~5 min | **Skill level:** Intermediate-Advanced | **Platform:** iOS & Android

You've used Claude Code in your terminal. Now put it in your **pipeline.** Automated PR analysis, code fixes, and issue triage — all triggered by GitHub events.

> "If Claude can review code in my terminal, why can't it review every PR automatically?"

It can. Here's how.

---

## 🎯 GitHub Actions — `claude-code-action`

Anthropic publishes an **official GitHub Action** that runs Claude Code inside your CI pipeline. It reads diffs, posts inline comments, and can even push fixes.

### Quick Setup

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]
  issue_comment:
    types: [created]

jobs:
  claude-review:
    if: |
      github.event_name == 'pull_request' ||
      (github.event_name == 'issue_comment' &&
       contains(github.event.comment.body, '@claude'))
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      issues: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

That's it. Every PR gets reviewed. Every `@claude` comment in a PR triggers a response.

---

## 📱 Mobile-Specific Pipeline

For TaskPulse, configure Claude to understand your mobile project:

```yaml
# .github/workflows/claude-mobile-review.yml
name: Claude Mobile Review
on:
  pull_request:
    paths:
      - '**/*.swift'
      - '**/*.kt'
      - '**/*.gradle*'
      - '**/Package.swift'

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_code_args: '--model claude-sonnet-4-6'
```

Add a `CLAUDE.md` at your repo root — the action reads it automatically, so Claude knows your architecture, patterns, and rules.

---

## 🖥️ Headless Mode for Custom Pipelines

Not using GitHub Actions? Run Claude Code directly in any CI system with `--headless` and `--print`:

```bash
# In any CI pipeline (Jenkins, GitLab CI, CircleCI, etc.)
claude --headless --print -p "Review the diff in this PR for:
1. Logic errors
2. Missing error handling
3. iOS/Android platform inconsistencies
4. Breaking changes to public APIs

Output as markdown with inline code references."
```

### Key CLI Flags for CI

| Flag | Purpose |
|------|---------|
| `--headless` | No interactive UI (required for CI) |
| `--print` | Output to stdout for capture/piping |
| `-p "prompt"` | Pass prompt inline (no TTY needed) |
| `--model` | Choose model (sonnet for speed, opus for depth) |
| `--max-tokens` | Cap output length |
| `--dangerously-skip-permissions` | Skip permission prompts (CI only!) |

**`--dangerously-skip-permissions`** is necessary in CI because there's no human to approve tool use. Only use it in sandboxed CI environments, never on developer machines.

---

## 🔄 GitLab CI/CD Integration

Claude Code works with GitLab's event-driven CI too:

```yaml
# .gitlab-ci.yml
claude-review:
  stage: review
  image: node:20
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  script:
    - npm install -g @anthropic-ai/claude-code
    - |
      claude --headless --print -p "
        Review this MR diff for the TaskPulse project.
        Focus on: architecture violations, missing tests, security issues.
        Post findings as MR comments.
      "
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY
```

### GitLab-Specific Patterns

| Pattern | Trigger | Use Case |
|---------|---------|----------|
| MR review | `merge_request_event` | Auto-review every MR |
| Issue implementation | `@claude` in issue comment | Claude picks up issues and creates MRs |
| Performance analysis | Scheduled pipeline | Weekly performance regression checks |
| Test generation | After test failures | Auto-generate missing test cases |

---

## 🏗️ Practical Pipeline Patterns

### Pattern 1: Review + Fix

Claude reviews the PR AND pushes fixes for simple issues:

```yaml
steps:
  - uses: anthropics/claude-code-action@v1
    with:
      anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
      # Claude can push fix commits for lint/formatting issues
      claude_code_args: '--model claude-sonnet-4-6'
```

### Pattern 2: Path-Specific Reviews

Different review rules for different parts of the codebase:

```yaml
jobs:
  ios-review:
    if: contains(github.event.pull_request.changed_files, '.swift')
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Uses Sources/CLAUDE.md for iOS-specific rules

  android-review:
    if: contains(github.event.pull_request.changed_files, '.kt')
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Uses app/CLAUDE.md for Android-specific rules
```

### Pattern 3: Issue Triage

Auto-label and assign incoming issues:

```yaml
on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

---

## ⚠️ Security Considerations

| Risk | Mitigation |
|------|------------|
| API key exposure | Use GitHub Secrets / GitLab CI Variables — never hardcode |
| Excessive permissions | Grant only `contents: read` + `pull-requests: write` |
| Runaway costs | Set `--max-tokens` and use `claude-sonnet-4-6` for reviews |
| Untrusted input | Don't run Claude on PRs from forks with write access |
| `--dangerously-skip-permissions` | Only in sandboxed CI runners, never locally |

---

## 🧪 Try It Now

1. **Add the GitHub Action.** Copy the quick setup YAML to `.github/workflows/claude-review.yml`. Set your `ANTHROPIC_API_KEY` secret. Open a PR and watch Claude review it.

2. **Customize with CLAUDE.md.** Add your project's architecture and code style rules. Claude reads them during review — same as in your terminal.

3. **Test headless mode locally.** Run `claude --headless --print -p "List all files in this repo"` to verify headless works before adding to CI.

4. **Add path filters.** Restrict Claude reviews to specific file types (`.swift`, `.kt`) so you don't burn tokens on config file changes.

---

← [Previous: Create PRD](../Part%205%20—%20Daily%20Scenarios/25-create-prd.md) | [Course Map](../00-course-map.md) | [Next: Automated Code Review →](27-code-review.md)
