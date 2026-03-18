# 🔗 Resources & Further Reading

> Curated links to level up your agentic coding game. Focused on mobile dev + AI.

---

## 📚 Official Documentation

| Resource | Link | What You'll Learn |
|----------|------|-------------------|
| Gemini CLI Docs | [geminicli.com/docs](https://geminicli.com/docs) | Everything Gemini CLI |
| GEMINI.md Guide | [blog.google/technology/google-deepmind/using-gemini-md-files](https://blog.google/technology/google-deepmind/using-gemini-md-files) | Memory file best practices |
| Agents Docs | [geminicli.com/docs/agents](https://geminicli.com/docs/agents) | Creating reusable agents |
| Hooks Guide | [geminicli.com/docs/hooks-guide](https://geminicli.com/docs/hooks-guide) | Automating with hooks |
| Multi-agent | [geminicli.com/docs/multi-agent](https://geminicli.com/docs/multi-agent) | Multi-agent orchestration |
| Memory & Context | [geminicli.com/docs/memory](https://geminicli.com/docs/memory) | How Gemini remembers |
| MCP Docs | [geminicli.com/docs/tools/mcp-server](https://geminicli.com/docs/tools/mcp-server) | Model Context Protocol |
| MCP Specification | [modelcontextprotocol.io](https://modelcontextprotocol.io) | Full MCP spec |
| Extensions | [geminicli.com/docs/extensions](https://geminicli.com/docs/extensions) | Gemini CLI extensions |
| Building Effective Agents | [ai.google.dev/gemini-api/docs/agents](https://ai.google.dev/gemini-api/docs/agents) | Google's agent patterns |

---

## 🛠️ MCP Servers for Mobile Devs

### 🏆 Must-Have
| Server | What It Does | Setup |
|--------|-------------|-------|
| **GitHub MCP** | PR management, code search, issues | `gemini mcp add github-mcp-server` |
| **Atlassian/Jira** | Ticket management, Confluence docs | [atlassian.com/blog/announcements/remote-mcp-server](https://www.atlassian.com/blog/announcements/remote-mcp-server) |
| **Linear** | Fast ticket management | [linear.app/developers](https://linear.app/developers) |

### 🎨 Design & UI
| Server | What It Does | Setup |
|--------|-------------|-------|
| **Figma MCP** | Design tokens, component specs | `gemini mcp add figma-mcp` |
| **Storybook MCP** | Component catalog access | Community MCP |

### 📊 Monitoring & Analytics
| Server | What It Does | Setup |
|--------|-------------|-------|
| **Sentry MCP** | Error tracking, crash reports | `gemini mcp add sentry-mcp` |
| **Firebase MCP** | Analytics, push, remote config | `gemini mcp add firebase-mcp` |

### 🔧 Infrastructure
| Server | What It Does | Setup |
|--------|-------------|-------|
| **Docker MCP Toolkit** | 200+ containerized servers | [docker.com/blog/add-mcp-servers-to-gemini-cli-with-mcp-toolkit](https://www.docker.com/blog/add-mcp-servers-to-gemini-cli-with-mcp-toolkit/) |
| **Composio** | Multi-service connector | `npx @composio/mcp@latest setup` |

### 📂 MCP Directories
- [PulseMCP Server Directory](https://www.pulsemcp.com/servers) — Browse 10,000+ MCP servers
- [MCP.so](https://mcp.so) — Another comprehensive directory
- [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers) — GitHub curated list

---

## 📖 Best Guides & Articles

### 🧠 GEMINI.md & Configuration
- [Writing a Good GEMINI.md](https://www.humanlayer.dev/blog/writing-a-good-gemini-md) — HumanLayer deep dive
- [The Complete Guide to AI Agent Memory Files](https://medium.com/data-science-collective/the-complete-guide-to-ai-agent-memory-files-claude-md-agents-md-and-beyond-49ea0df5c5a9) — GEMINI.md, AGENTS.md & beyond
- [Creating the Perfect GEMINI.md](https://dometrain.com/blog/creating-the-perfect-geminimd-for-gemini-cli/) — Dometrain's approach
- [Every AI Config File Explained](https://www.deployhq.com/blog/ai-coding-config-files-guide) — GEMINI.md vs AGENTS.md vs others

### ⚡ Agents, Commands & Hooks
- [Gemini CLI Agents, Commands, Hooks Guide](https://genaiunplugged.substack.com/p/gemini-cli-agents-commands-hooks) — Comprehensive tutorial
- [Making Agents Activate Reliably](https://scottspence.com/posts/how-to-make-gemini-cli-agents-activate-reliably) — Scott Spence's tips
- [Agents vs Commands vs Subagents vs Extensions](https://www.youngleaders.tech/p/gemini-agents-commands-subagents-extensions) — Decision framework
- [Gemini CLI Hooks Mastery](https://github.com/disler/gemini-cli-hooks-mastery) — GitHub repo with examples

### 🐝 Multi-agent & Swarms
- [Gemini CLI Multi-agent: Complete Guide](https://claudefa.st/blog/guide/agents/multi-agent) — Detailed walkthrough
- [From Tasks to Swarms](https://alexop.dev/posts/from-tasks-to-swarms-multi-agent-in-gemini-cli/) — Evolution of agent work
- [Gemini CLI Swarms](https://addyosmani.com/blog/gemini-cli-multi-agent/) — Addy Osmani's overview

### 🔄 Ralph Loops & Autonomy
- [Getting Started With Ralph](https://www.aihero.dev/getting-started-with-ralph) — Beginner's guide
- [The RALPH Loop Changes Everything](https://medium.com/@munish.munagala/using-gemini-cli-the-ralph-loop-changes-everything-part-1-7c842d4a5352) — Deep dive
- [Ralph Wiggum Technique](https://www.atcyrus.com/stories/ralph-wiggum-technique-gemini-cli-autonomous-loops) — Autonomous loops explained
- [My RALPH Workflow](https://adamtuttle.codes/blog/2026/my-ralph-workflow-for-gemini-cli/) — Practical workflow
- [Ralph Gemini CLI Plugin](https://github.com/frankbria/ralph-gemini-cli) — GitHub repo

### 📋 PRD & Spec-Driven Development
- [How to Write PRDs for AI Coding Agents](https://medium.com/@haberlah/how-to-write-prds-for-ai-coding-agents-d60d72efb797) — PRD best practices
- [How to Write a Good Spec for AI Agents](https://addyosmani.com/blog/good-spec/) — Addy Osmani (O'Reilly)
- [AI PRD Workflow](https://github.com/nurettincoban/ai-prd-workflow) — GitHub template repo
- [PRDs in the AI Era](https://www.news.aakashg.com/p/ai-prd) — Modern approach
- [PRDs with Gemini CLI](https://www.chatprd.ai/learn/PRD-for-Gemini-CLI) — Gemini-specific guide

### 🤖 Agentic Coding Philosophy
- [Introduction to Agentic Coding](https://blog.google/technology/google-deepmind/introduction-to-agentic-coding) — Google DeepMind's vision
- [The Future of Agentic Coding](https://addyosmani.com/blog/future-agentic-coding/) — Conductors to orchestrators
- [AI Coding Agents: Coherence Through Orchestration](https://mikemason.ca/writing/ai-coding-agents-jan-2026/) — Why orchestration > autonomy
- [My LLM Coding Workflow](https://addyosmani.com/blog/ai-coding-workflow/) — Addy Osmani's 2026 workflow
- [2026 Agentic Coding Trends Report](https://ai.google.dev/gemini-api/2026-agentic-coding-trends-report.pdf) — Google's data

---

## 🐙 GitHub Repos

| Repo | What It Is |
|------|-----------|
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Official Gemini CLI repository |
| [Gemini CLI Hooks Mastery](https://github.com/disler/gemini-cli-hooks-mastery) | Hook examples and patterns |
| [Ralph Gemini CLI](https://github.com/frankbria/ralph-gemini-cli) | Ralph Loop plugin |
| [AI PRD Workflow](https://github.com/nurettincoban/ai-prd-workflow) | PRD → RFC → Code templates |
| [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers) | MCP server directory |

---

## 📱 Mobile + AI Specific

- [AI-Assisted Mobile Development 2026](https://dev.to/devin-rosario/how-ai-assistants-are-transforming-mobile-app-development-in-2026-18en) — Trends overview
- [Gemini CLI on Mobile](https://www.builder.io/blog/gemini-cli-mobile-phone) — Using Gemini CLI from phone
- [Happy Coder](https://happy.engineering/) — Mobile client for Gemini CLI
- [State of AI vs Human Code](https://www.coderabbit.ai/blog/state-of-ai-vs-human-code-generation-report) — Bug rate comparison data

---

## 📊 Key Statistics to Remember

| Stat | Source |
|------|--------|
| AI creates **1.7× more total bugs** than humans | CodeRabbit 2026 Report |
| AI creates **1.5-2× more security bugs** | CodeRabbit 2026 Report |
| **71%** of devs don't merge AI code without review | Stack Overflow Survey |
| Frontier LLMs follow **150-200 instructions** reliably | Google DeepMind Research |
| **10,000+** public MCP servers available | MCP Ecosystem Data |
| **60,000+** repos use AGENTS.md | Linux Foundation |
| **40%** of enterprise apps will embed AI agents by 2026 | Gartner |

---

> 🎓 **Remember:** The goal isn't to use ALL of these tools — it's to pick the right ones for your workflow and build on them gradually. Start with GEMINI.md, add agents when you see patterns, and grow from there.

---

[← B: Prompt Library](B-prompt-library.md) | [🗺️ Course Map](../00-course-map.md)
