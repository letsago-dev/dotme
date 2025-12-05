# Claude Code Complete Guide

> Claude understands your codebase, makes edits with your permission, and executes commands — right from your terminal.

## Getting Help

- **?** — Show Claude help
- **/help** — See available commands and tips

## Quick Reference

| Feature | When | How | Example |
|---------|------|-----|---------|
| **Commands** | Typing same instructions repeatedly | `.claude/commands/name.md` | Code review checklist |
| **Memories** | Remember project-specific info | Tell Claude "remember that..." | Architecture decisions |
| **Prompts** | Occasional complex instructions | `.claude/prompts/name.md` | PR description template |
| **Skills** | Complex multi-step tasks | `skill-name/SKILL.md` with frontmatter | PDF processing |
| **Personas** | Domain-specific guidance | `.claude/agents/name.md` | Security auditor, Python coach |
| **Subagents** | Focused analysis, investigation | Claude launches via Task tool | Explore, Plan |
| **Zen MCP** | Deep analysis, consensus | `mcp__zen__` tools | Multi-model consensus |

## Plan vs Edit Modes (shift + tab)

Claude Code turns specs into working code. Point it at a requirement (Notion PRD, Linear issue, GitHub issue), and it will analyze the spec, explore the codebase, and build a plan. Once you approve the plan, it implements the changes, runs tests, and commits.

**Plan Mode**: Claude reads specs, inspects the codebase, and asks clarifying questions until the solution is clear. It writes a step-by-step implementation plan (saved to `.claude/`) but makes no changes. When ready, Claude prompts you to switch to Edit Mode.

**Edit Mode**: Claude implements the plan — adds, refactors, and removes code, runs tests, and commits changes.

## Keyboard Shortcuts

| Key | What it does |
|-----|--------------|
| `shift + ⏎` | Insert newline in prompt |
| `double tap esc` | Clear input field |
| `ctrl + _` | Undo last action (repeatable, like editor undo) |
| `ctrl + z` | Suspend session to shell (use `fg` to resume) |
| `ctrl + o` | Toggle verbose output (debug Claude's process) |
| `ctrl + v` | Paste images — Claude analyzes screenshots, diagrams, visual context |
| `ctrl + g` | Vim keybindings in input area |
| `ctrl + t` | Show todos (current task list) |
| `tab` | Toggle thinking mode — see Claude's reasoning for complex problems |

## Special Characters

### ! - Bash Mode
Execute shell commands directly without asking Claude.

**Examples**:
```
!git status
# Shows git status immediately

!pytest tests/
# Runs test suite directly

!docker ps
# Lists running containers
```

**When to use**:
- Quick shell commands
- No need for Claude's analysis
- Direct terminal interaction

### @ - File Paths
Reference files for Claude to read or analyze.

**Examples**:
```
Review @src/main.py
# Claude reads and reviews the file

Explain @config/settings.json
# Claude reads and explains configuration

Compare @old/version.py @new/version.py
# Claude compares two files
```

**File access**:
- Any file in your working directory
- Paths relative to current directory
- Use absolute paths if needed: @/absolute/path/file.txt

### # - Memorize
Tell Claude to remember information across sessions.

**Examples**:
```
# Remember that we use pytest for testing
# Our API base URL is https://api.example.com
# The project lead is Alice
```

**What happens**:
- Claude stores this in memory
- Available in future sessions
- Stored in `~/.claude/memory/` (not version controlled)
- Use `/memory` to view/manage memories

### / - Commands
Execute built-in or custom commands.

**Built-in**: `/help`, `/agents`, `/memory`
**Custom**: `/your-command` (from `.claude/commands/`)

## Built-In Commands

| Group | Command | What it does | When to use |
|-------|---------|--------------|-------------|
| Project | `/add-dir` | Add directories to context | Working across multiple dirs |
| Project | `/context` | Show current context | See what Claude can access |
| Session | `/clear` | Clear history, keep session | Start fresh discussion |
| Session | `/exit` | Terminate session | Done working |
| Session | `/compact` | Compress history | Long sessions, token limits |
| Execution | `/bashes` | List background processes | See what's still running |
| Execution | `/resume` | Resume previous session | Continue earlier work |
| Execution | `/rewind` | Go back in conversation | Undo multiple steps |
| Tools | `/agents` | Manage custom agents | Need domain expert, reviewer |
| Tools | `/hooks` | Configure event-triggered scripts | Auto-logging, validation |
| Tools | `/mcp` | Manage MCP servers | Connect external tools |
| Tools | `/plugin` | Manage plugins | Extend functionality |
| Tools | `/vim` | Configure vim integration | Editor setup |
| Memory | `/memory` | View/manage memories | Review or remove stored context |
| Memory | `/todos` | View task list (also `ctrl+t`) | See what Claude is tracking |
| Security | `/permissions` | Manage file/command permissions | Control Claude's access |
| Security | `/sandbox` | Enable/disable sandbox | Restrict actions for safety |
| Security | `/securityreview` | Security-focused code review | Audit for vulnerabilities |
| Review | `/review` | Comprehensive code review | Pre-commit quality check |
| Review | `/init` | Initialize Claude in project | First-time repo setup |
| UI | `/statusline` | Configure status line | Customize status bar |
| UI | `/export` | Export conversation | Save or share discussion |
| UI | `/usage` | Show token stats | Monitor API usage, costs |

See also: `agents/README.md`, `hooks/README.md`

## Zen MCP Tools

Advanced AI workflows via Zen MCP server.

| Command | What it does | When to use |
|---------|--------------|-------------|
| `/zen:chat` | Collaborative thinking with external AI | Brainstorming, second opinions |
| `/zen:thinkdeep` | Multi-stage deep reasoning | Complex bugs, architecture decisions |
| `/zen:consensus` | Multi-model structured debate | Important decisions, trade-offs |
| `/zen:planner` | Interactive task breakdown | Complex projects, migrations |
| `/zen:review` | Systematic code review | Pre-commit quality, security, perf |
| `/zen:analyse` | Comprehensive code analysis | Architecture, maintainability |
| `/zen:testgen` | Generate test suites | Tests with edge case coverage |
| `/zen:challenge` | Force critical thinking | Challenge earlier answers |

## Typical Workflows

### Quick Edit
```
1. Ask Claude to make changes
2. Claude edits files
3. Review changes (git diff)
4. Accept or request modifications
```

### Research and Plan
```
1. Switch to plan mode (shift + tab)
2. Ask Claude to analyze problem
3. Claude presents plan
4. Approve or refine plan
5. Claude executes approved plan
```

### Code Review
```
1. /review (or /zen:review for comprehensive)
2. Claude analyzes code
3. Provides findings with severity
4. Suggests improvements
5. You implement or request changes
```

### Testing
```
1. Generate tests: /zen:testgen
2. Claude analyzes code, creates test suite
3. Run tests: !pytest
4. Fix failures
5. Check coverage
```

## Custom Extensions

### Commands
Simple prompt templates, accessed via `/command-name`.

See: `commands/README.md`

### Prompts
Specialized templates for occasional use.

See: `prompts/README.md`

### Skills
Complex multi-step workflows with tool access.

See: `skills/README.md`

### Agents
Domain expert personas for specialized guidance.

See: `agents/README.md`

### Hooks
Shell commands triggered by events.

See: `hooks/README.md`

## Directory Structure

```
~/.me/claude/          → Your version-controlled configs
  ├── agents/          → Custom AI personas
  ├── commands/        → Quick prompt templates
  ├── prompts/         → Specialized templates
  ├── skills/          → Multi-step workflows
  ├── hooks/           → Event-triggered scripts
  ├── CLAUDE.md        → Your global instructions
  └── .me.md           → Quick reference (this context)

~/.claude/             → Symlinks to ~/.me/claude/
  ├── history.jsonl    → Session history (not tracked)
  ├── memory/          → Stored memories (not tracked)
  ├── debug/           → Debug logs (not tracked)
  └── ...              → Other runtime files
```

**Version controlled**: agents, commands, skills, prompts, hooks, CLAUDE.md
**Not tracked**: history, memory, debug, sessions, temp files

## Tips and Tricks

### Efficient Prompting
- Be specific about what you want
- Reference files with @
- Use shift+tab for complex tasks
- Break large tasks into steps

### Using Context
- `@` to include files
- `#` to save facts for later
- `/memory` to review remembered context
- `/context` to see current scope

### Workflow Patterns
- **Exploration**: Plan mode + thinking
- **Execution**: Edit mode + direct commands
- **Review**: /review or /zen:review
- **Complex**: /zen:planner → approve → execute

### Error Recovery
- ctrl+_ to undo
- /rewind to go back further
- /clear to start fresh
- /resume to continue earlier work

## Learning More

- Official docs: https://code.claude.com/docs
- Project README: `~/.me/README.md`
- Quick start: `~/.me/QUICKSTART.md`
- Subdirectory READMEs for specific features

## Getting Started

1. Explore available commands: `/`
2. Review this guide: `cat ~/.claude/README.md`
3. Try plan mode: shift+tab
4. Create your first command: `vim ~/.claude/commands/my-command.md`
5. Configure hooks for automation: `/hooks`
6. Experiment with zen tools: `/zen:chat "question"`
