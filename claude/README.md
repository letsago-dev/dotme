# Claude Code Complete Guide

> Claude understands your codebase, makes edits with your permission, and executes commands — right from your terminal.

## Quick Reference

- **Shift+Tab**: Cycle between edit mode and plan mode
- **?**: Show Claude help

### When and How to Use

| Feature | When | How | Example |
|---------|------|-----|---------|
| **Commands** | Typing same instructions repeatedly | `.claude/commands/name.md` | Code review checklist |
| **Memories** | Remember project-specific info | Tell Claude "remember that..." | Architecture decisions |
| **Prompts** | Occasional complex instructions | `.claude/prompts/name.md` | PR description template |
| **Skills** | Complex multi-step tasks | `skill-name/SKILL.md` with frontmatter | PDF processing |
| **Personas** | Domain-specific guidance | `.claude/agents/name.md` | Security auditor, Python coach |
| **Subagents** | Focused analysis, investigation | Claude launches via Task tool | Explore, Plan |
| **Zen MCP** | Deep analysis, consensus | `mcp__zen__` tools | Multi-model consensus |

## Getting Help

Type `/help` in any Claude session to see available commands and tips.

## Prompting Tips

### Input Controls
- **shift + ⏎**: Insert newline in your prompt (multi-line input)
- **shift + tab**: Switch between plan mode and edit mode
- **double tap esc**: Clear input field

### Plan vs Edit Modes

**Plan Mode** (shift + tab to activate):
- Claude researches and plans before executing
- Reviews approach with you first
- Best for: complex tasks, exploration, when unsure

**Edit Mode** (default):
- Claude executes immediately
- Makes changes as you request
- Best for: clear instructions, known workflows

### Undo and Control
- **ctrl + _**: Undo last action
  - Reverts the most recent file change or operation
  - Works like editor undo, can be repeated
  - Useful when Claude makes unwanted changes
- **ctrl + z**: Suspend Claude session
  - Returns you to shell (like suspending any terminal app)
  - Use `fg` to resume the Claude session
  - Different from `/exit` - doesn't terminate Claude

### Output and Input
- **ctrl + o**: Toggle verbose output
  - Shows detailed information about what Claude is doing
  - Useful for debugging or understanding Claude's process
- **ctrl + v**: Paste images
  - Attach screenshots, diagrams, or visual context
  - Claude can analyze and discuss images
- **ctrl + g**: Vim mode
  - Enables vim keybindings in the input area
  - For vim users who want familiar navigation

### Workflow Controls
- **ctrl + t**: Show todos
  - Displays current task list
  - See what Claude is tracking and working on
- **tab**: Toggle thinking mode
  - **Without thinking**: Claude responds directly
    - Faster responses
    - Good for simple, clear requests
  - **With thinking**: Claude shows reasoning process
    - See Claude's analysis and decision-making
    - Understand why Claude chose specific approaches
    - Useful for complex problems or learning

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

### Project Management

#### /add-dir
Add additional directories to Claude's context.

**Use case**: Working across multiple directories
```
/add-dir ../shared-lib
```

#### /context
Show current context (files, directories, settings).

**Use case**: Understand what Claude can see
```
/context
```

### Session Control

#### /clear
Clear conversation history but keep session active.

**Use case**: Start fresh discussion without restarting
```
/clear
```

**Note**: Different from `/exit` - session continues, just clears history

#### /exit
Terminate Claude session completely.

```
/exit
```

#### /compact
Compress conversation history to reduce token usage.

**Use case**: Long sessions approaching token limits
```
/compact
```

### Execution Management

#### /bashes
List all background bash processes.

**Use case**: See what commands are still running
```
/bashes
```

#### /resume
Resume a previous session.

**Use case**: Continue work from earlier session
```
/resume
```

#### /rewind
Go back to an earlier point in conversation.

**Use case**: Undo multiple steps or restart from checkpoint
```
/rewind
```

### Tools and Integrations

#### /agents
Create or manage custom agents (specialized AI personas).

**Use case**: Need domain expert, code reviewer, security auditor
```
/agents
```

See: `agents/README.md`

#### /hooks
Configure hooks (shell commands triggered by events).

**Use case**: Auto-logging, validation, custom automation
```
/hooks
```

See: `hooks/README.md`

#### /mcp
Manage Model Context Protocol servers.

**Use case**: Connect external tools and services
```
/mcp
```

#### /plugin
Manage Claude Code plugins.

**Use case**: Extend functionality
```
/plugin
```

#### /vim
Configure vim integration.

**Use case**: Set up editor integration
```
/vim
```

### Memory and State

#### /memory
View and manage Claude's memories.

**Use case**: See what Claude remembers, remove outdated info
```
/memory
```

**What's stored**:
- Facts you told Claude to remember (#)
- Project-specific context
- Preferences and standards

#### /todos
View current task list.

**Use case**: See what Claude is tracking
```
/todos
```

Also accessible via: **ctrl + t**

### Security and Permissions

#### /permissions
Review and manage file/command permissions.

**Use case**: Control what Claude can access
```
/permissions
```

#### /sandbox
Enable/disable sandbox mode.

**Use case**: Restrict Claude's actions for safety
```
/sandbox
```

#### /securityreview
Trigger security-focused code review.

**Use case**: Audit code for vulnerabilities
```
/securityreview
```

### Analysis and Review

#### /review
Comprehensive code review.

**Use case**: Pre-commit review, quality check
```
/review
```

#### /init
Initialize Claude in a new project.

**Use case**: Set up Claude for first time in repo
```
/init
```

### Display and UI

#### /statusline
Configure status line display.

**Use case**: Customize what's shown in Claude's status bar
```
/statusline
```

#### /export
Export conversation or data.

**Use case**: Save discussion, share with team
```
/export
```

#### /usage
Show token usage statistics.

**Use case**: Monitor API usage, track costs
```
/usage
```

## Zen MCP Tools

Advanced AI workflows via Zen MCP server.

### /zen:chat
General collaborative thinking partner.

**Use case**: Brainstorming, getting second opinions, exploring ideas
```
/zen:chat
```

**What it does**: Launches external AI model for discussion

### /zen:thinkdeep
Multi-stage investigation and deep reasoning.

**Use case**: Complex bugs, architecture decisions, performance analysis
```
/zen:thinkdeep
```

**What it does**: Systematic hypothesis testing and investigation

### /zen:consensus
Multi-model consensus through structured debate.

**Use case**: Important decisions, architectural choices, evaluating trade-offs
```
/zen:consensus
```

**What it does**: Consults multiple AI models, synthesizes recommendations

### /zen:planner
Interactive task breakdown and planning.

**Use case**: Complex projects, system design, migration strategies
```
/zen:planner
```

**What it does**: Builds plans incrementally with deep reflection

### /zen:review (also /zen:codereview)
Systematic code review with expert validation.

**Use case**: Pre-commit review covering quality, security, performance
```
/zen:review
```

**What it does**: Structured investigation ensuring thoroughness

### /zen:analyse (also /zen:analyze)
Comprehensive code analysis.

**Use case**: Architecture, performance, maintainability analysis
```
/zen:analyse
```

**What it does**: Systematic investigation with expert validation

### /zen:testgen
Generate comprehensive test suites.

**Use case**: Create tests with edge case coverage
```
/zen:testgen
```

**What it does**: Analyzes code paths, generates framework-specific tests

### /zen:challenge
Prevents reflexive agreement, forces critical thinking.

**Use case**: When user critically questions or challenges earlier answers
```
/zen:challenge <statement>
```

**What it does**: Scrutinizes claims, prevents automatic agreement

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
- @ to include files
- # to save facts for later
- /memory to review remembered context
- /context to see current scope

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
