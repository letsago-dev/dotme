# Claude Code Complete Guide

> Claude understands your codebase, makes edits with your permission, and executes commands — right from your terminal.

## Quick Help (type `?`)

### Special Characters

| Key | What it does | Details | Example |
|-----|--------------|---------|---------|
| `/` | Commands | Built-in (see below) and custom (`claude/commands/`) | `/review` |
| `@` | File paths | Reference files for Claude to read | `@README.md` |
| `#` | Memorize | Save facts for future sessions in `~/.claude/memory/` | `# always run tests before commit` |

#### `!` vs `&`

| | `!` Bash mode | `&` Background |
|---|---|---|
| **Execution** | Runs immediately, blocks until done | Runs in background, returns immediately |
| **Claude** | Bypasses Claude entirely | Claude tracks it via `/tasks` |
| **Use case** | Quick commands you want to see output | Long-running processes (servers, tests) |
| **Example** | `!git status` | `&npm run dev` |

### Keyboard Shortcuts

| Key | What it does | Details |
|-----|--------------|---------|
| `double tap esc` | Clear input | |
| `shift + tab` | Toggle mode | Switch between Plan and Edit modes (see below) |
| `shift + ⏎` | Newline | Insert newline in prompt |
| `tab` | Toggle thinking | See Claude's reasoning for complex problems |
| `ctrl + _` | Undo | Repeatable, like editor undo |
| `ctrl + z` | Suspend | Returns to shell, use `fg` to resume |
| `ctrl + o` | Verbose output | Debug Claude's process |
| `ctrl + t` | Show todos | Current task list |
| `ctrl + g` | Vim mode | Vim keybindings in input area |
| `ctrl + v` | Paste images | Claude analyzes screenshots, diagrams, visual context |

## Plan vs Edit Modes (shift + tab)

Claude Code turns specs into working code. Point it at a requirement (Notion PRD, Linear issue, GitHub issue), and it will analyze the spec, explore the codebase, and build a plan. Once you approve the plan, it implements the changes, runs tests, and commits.

**Plan Mode**: Claude reads specs, inspects the codebase, and asks clarifying questions until the solution is clear. It writes a step-by-step implementation plan (saved to `.claude/`) but makes no changes. When ready, Claude prompts you to switch to Edit Mode.

**Edit Mode**: Claude implements the plan — adds, refactors, and removes code, runs tests, and commits changes.

## Expanded Help (type `/help`)

| Group | Command | What it does |
|-------|---------|--------------|
| Project | `/init` | Initialize a new CLAUDE.md file with codebase documentation (see below) |
| Project | `/add-dir` | Add a new working directory (see below) |
| Context | `/context` | Visualize current context usage as a colored grid |
| Context | `/clear` | Clear conversation history and free up context |
| Context | `/compact` | Clear history but keep a summary (optional: `/compact [instructions]`) |
| Session | `/exit` | Exit the REPL |
| Session | `/resume` | Resume a conversation (see below) |
| Session | `/rewind` | Restore code and/or conversation to a previous point (see below) |
| Session | `/plan` | View or open the current session plan |
| Tasks | `/tasks` | List and manage background tasks |
| Tasks | `/todos` | List current todo items |
| Tools | `/agents` | Manage agent configurations (see below) |
| Tools | `/hooks` | Manage hook configurations for tool events (see below) |
| Tools | `/mcp` | Manage MCP servers |
| Tools | `/plugin` | Manage Claude Code plugins (see below) |
| Tools | `/ide` | Manage IDE integrations and show status (see below) |
| Tools | `/vim` | Toggle between Vim and Normal editing modes |
| Security | `/permissions` | Manage allow & deny tool permission rules (see below) |
| Security | `/sandbox` | Configure sandbox mode (see below) |
| Review | `/review` | Review a pull request |
| Review | `/pr-comments` | Get comments from a GitHub pull request |
| Account | `/login` | Sign in with your Anthropic account |
| Account | `/logout` | Sign out from your Anthropic account |
| Account | `/usage` | Show plan usage limits |
| Account | `/upgrade` | Upgrade to Max for higher rate limits |
| Settings | `/model` | Set the AI model for Claude Code |
| Settings | `/output-style` | Set the output style |
| Settings | `/statusline` | Set up Claude Code's status line UI |
| Settings | `/terminal-setup` | Install Shift+Enter key binding for newlines |
| Settings | `/remote-env` | Configure default remote environment for teleport |
| Info | `/help` | Show help and available commands |
| Info | `/status` | Show version, model, account, API connectivity |
| Info | `/doctor` | Diagnose and verify installation and settings |
| Info | `/release-notes` | View release notes |
| Info | `/export` | Export conversation to a file or clipboard |
| Info | `/feedback` | Submit feedback about Claude Code |

### About `/add-dir`

Expands Claude's context beyond your current working directory. By default, Claude only sees the directory you launched it from.

**When to use:**
- Monorepo: working in `packages/web` but need context from `packages/api`
- Frontend/backend split: in `~/frontend` but referencing `~/backend`
- Shared libraries: need to see `~/shared-lib` alongside your project

**Example:** `/add-dir ../backend` — now Claude can read files there (e.g., `@../backend/src/api.py`)

### About `/init`

Bootstraps a `CLAUDE.md` file for your project. Claude analyzes your codebase and generates project-specific context including build commands, code style conventions, and architecture notes.

**What it detects:**
- Package managers (package.json, pyproject.toml, Gemfile)
- Build tools (Makefile, Dockerfile, docker-compose)
- Test frameworks and lint configs
- Project structure patterns

**Scope:** Creates a *project-level* CLAUDE.md in the current directory, separate from your global `~/.claude/CLAUDE.md`.

### About `/resume`

When you exit Claude (`/exit` or close terminal), your conversation history is saved. Next time you run `claude`, you start fresh. Use `/resume` to pick up where you left off — same context, same files discussed, same task progress.

### About `/rewind`

Rolls back both code AND conversation to an earlier checkpoint. Unlike `ctrl + _` (which undoes the last file edit), `/rewind` goes back to a previous point in the entire session — undoing multiple file changes and removing conversation history after that point. Use when Claude went down a wrong path and you want to try a different approach.

### About `/agents`

Manages specialized AI personas stored in `.claude/agents/`. Agents are domain experts (security auditor, Python coach, architecture reviewer) that provide consistent, role-based guidance.

**What `/agents` does:**
- List available agents
- Create new agents interactively
- Edit or delete existing agents

**When to use agents:**
- Security audits with OWASP expertise
- Code reviews with consistent standards
- Mentoring with specific teaching style
- Domain analysis (performance, accessibility, etc.)

**Location:** `~/.claude/agents/*.md` (see `agents/README.md` for format)

### About `/hooks`

Configures shell commands that run automatically on Claude Code events. Hooks enable custom automation, validation, and integration with external tools.

**What `/hooks` does:**
- List configured hooks
- Create new hooks interactively
- Edit or delete existing hooks

**Available events:**
- `user-prompt-submit` — when you send a message (access `$PROMPT`)
- `tool-call` — before Claude runs a tool (access `$TOOL_NAME`, `$TOOL_ARGS`)
- `session-start` — when Claude Code starts

**Use cases:**
- Audit logging of all prompts
- Block dangerous commands (e.g., `rm -rf`)
- Show git status on session start
- Trigger external notifications

**Location:** `~/.claude/settings.json` (see `hooks/README.md` for examples)

### About `/plugin`

Manages Claude Code plugins — shareable packages that bundle commands, agents, skills, hooks, and MCP servers.

**What `/plugin` does:**
- Discover plugins from marketplaces
- Install, enable, disable, uninstall plugins
- Register custom marketplaces

**Plugin structure:**
```
.claude-plugin/
├── plugin.json      # Metadata (name, version, description)
├── commands/        # Slash commands
├── agents/          # Specialized personas
├── skills/          # Multi-step workflows
└── hooks/           # Event automation
```

**Use cases:**
- Share your Claude setup with teammates
- Install community extensions
- Package project-specific tooling

### About `/ide`

Shows status and manages IDE integrations. Claude Code can connect to editors for richer context and inline diffs.

**Supported IDEs:**
- VS Code, Cursor, Windsurf, VSCodium (extension or legacy CLI)
- JetBrains IDEs (plugin from Marketplace)

**What integration provides:**
- Share current file selection with Claude
- Display diffs in editor instead of terminal
- Access lint/syntax errors for better context

**Activation:** Runs automatically when you launch `claude` from your IDE's integrated terminal.

### About `/permissions`

Controls which tools Claude can use without asking. Tired of approving the same commands? Allow them. Want to block something dangerous? Deny it.

**Three levels:**
- **Allow** — Claude runs it without asking (e.g., `Bash(git status:*)`)
- **Ask** — Claude asks every time (default for most tools)
- **Deny** — Claude can't use it at all (e.g., `Bash(rm -rf:*)`)

**Examples:**
```
Allow: Bash(npm test:*)      → run any npm test command
Allow: Bash(git:*)           → run any git command
Deny:  Bash(rm -rf:*)        → block recursive deletes
Allow: Read(~/.me/**)        → read anything in .me
```

**Where rules live:** `~/.claude/settings.json` or project-level `.claude/settings.json`

### About `/sandbox`

Configures sandbox mode — OS-level isolation that restricts Claude's filesystem and network access for safer execution.

**What sandbox provides:**
- Filesystem isolation (can't modify `~/.bashrc`, system binaries, etc.)
- Network isolation (only approved domains)
- Fewer permission prompts (trust boundaries set upfront)

**How it works:**
- Uses bubblewrap (Linux) or Seatbelt (macOS) to isolate child processes
- Default boundary: your current working directory
- Violations trigger notifications with deny/allow options

**Configuration** (in `settings.json`):
- Filesystem rules (read/write paths)
- Network domains (approved hosts)
- `allowUnsandboxedCommands` for escape hatch

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
