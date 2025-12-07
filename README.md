# .me - Personal Development Environment

**Your Developer Avatar** - A version-controlled representation of your opinionated development setup.

**The insight:** Your development environment IS your workflow. Working with AI tools requires consistent preferences across sessions, quick context switching (terminal + editor + AI), and memory muscles for workflows. Managing preferences across multiple tools (Claude, Cursor), editors (LunarVim, VSCode), and terminals (iTerm, zsh) creates overwhelming cognitive load. `.me` solves this by making your configuration **explicit, portable, and composable** through a plugin-based architecture:

### 1. Track Preferences Across Multiple Tools
Version control your dotfiles in a single repo:
- Claude agents, commands, skills, prompts
- Editor configs (LunarVim, Cursor, VSCode)
- Terminal setups (iTerm, zsh, fish)
- **Commit once, sync everywhere**

### 2. Document Tool Installations
Plugins check if tools are installed and provide install commands. New machine setup becomes:
```bash
git clone git@github.com:you/dotme.git ~/.me && cd ~/.me && ./itsame
```

### 3. Profile Switching
Switch between setups effortlessly:
- **Personal vs Work**: `./itsame work`
- **Per-Project**: `./itsame project-x`
- **Experiment with Others**: `./itsame mario git@github.com:mario/dotme.git`
- **Back to yours**: `cd ~/.me && ./itsame`

### 4. Shareable Developer Persona
Your `.me` becomes your **developer identity**:
- Share your opinionated setup with teammates
- Let others experiment with your preferences without breaking theirs
- Learn from others by "becoming them" temporarily
- Build organizational baselines for team consistency

## Quick Start

First, [fork letsago-dev/dotme](https://github.com/letsago-dev/dotme/fork) on GitHub.

```bash
# 1. Clone your fork as your avatar
git clone git@github.com:YOUR_USERNAME/dotme.git ~/.me
cd ~/.me

# 2. Add upstream for updates
git remote add upstream https://github.com/letsago-dev/dotme.git

# 3. Activate
./itsame
```

Your fork's `main` branch IS your avatar. See [Receiving Updates](#receiving-updates) when upstream has improvements you want.

## Receiving Updates

When letsago/dotme has improvements you want:

```bash
cd ~/.me
git pull upstream main
git push
```

## Contributing Back

If you create something useful for everyone:

```bash
cd ~/.me
git checkout -b feature/my-improvement

# Make changes, commit, push
git push -u origin feature/my-improvement

# Open PR from your fork to letsago-dev/dotme
git checkout main
```

## Structure

```
~/.me/
├── plugins/             # Self-contained tool scripts
│   ├── claude.sh         # Manages Claude symlinks
│   ├── lunarvim.sh       # Manages LunarVim symlinks
│   └── iterm.sh          # Manages iTerm symlinks
├── claude/              # Your Claude artifacts
│   ├── agents/           → ~/.claude/agents
│   ├── commands/         → ~/.claude/commands
│   ├── skills/           → ~/.claude/skills
│   ├── prompts/          → ~/.claude/prompts
│   ├── hooks/            → ~/.claude/hooks
│   └── CLAUDE.md         → ~/.claude/CLAUDE.md
├── config/
│   └── lvim/             → ~/.config/lvim
├── iterm/                → ~/.iterm
├── itsame               # Plugin orchestrator & profile switcher
├── rollback             # Restore original configs
└── README.md            # This file
```

## What Gets Tracked

### Claude Code
✅ **Tracked**: agents, commands, skills, prompts, hooks, CLAUDE.md

❌ **Ignored**: session history, debug logs, file-history, todos, shell-snapshots, settings

### LunarVim
✅ **Tracked**: Entire config/lvim directory

### iTerm
✅ **Tracked**: iTerm configuration and workflow documentation

## How It Works

### Plugin-Based Architecture

Each plugin is a self-contained bash script that:
1. Checks if the tool is installed (warns if missing, doesn't fail)
2. Backs up existing configs to `~/.me.bkp/` (one-time backup)
3. Creates symlinks from your home directory to `.me/` (idempotent)

**Plugin Template:**
```bash
#!/usr/bin/env bash
set -euo pipefail

# 1. Installation check
if ! command -v TOOL &> /dev/null; then
    echo "📥 TOOL not installed. Install via: <install command>"
    exit 0
fi

# 2. Backup existing config (idempotent)
if [[ -e "$HOME/.config/TOOL" ]] && \
   [[ ! -L "$HOME/.config/TOOL" ]] && \
   [[ ! -e "$BACKUP_DIR/config/TOOL" ]]; then
    echo "📦 Backing up TOOL config"
    mkdir -p "$BACKUP_DIR/config"
    mv "$HOME/.config/TOOL" "$BACKUP_DIR/config/TOOL"
fi

# 3. Create symlinks (idempotent)
mkdir -p "$HOME/.config"
ln -sf "$PROFILE_DIR/TOOL" "$HOME/.config/TOOL"

echo "✓ TOOL configured"
```

### Profile Activation

```bash
cd ~/.me
./itsame  # Discovers and runs all plugins in plugins/
```

What happens:
1. Auto-discovers plugins in `plugins/` directory
2. Each plugin backs up original configs to `~/.me.bkp/` (if not already backed up)
3. Creates symlinks: `~/.claude/agents` → `~/.me/claude/agents`, etc.
4. Idempotent - safe to run multiple times

## Workflow

### Making Changes

```bash
cd ~/.me

# Edit and commit atomically
lvim claude/commands/my-command.md
git add claude/commands/my-command.md
git commit -m "added new command"

lvim config/lvim/config.lua
git add config/lvim/config.lua
git commit -m "updated colorscheme to catppuccin"

git push
```

### Syncing Across Machines

```bash
# On machine 1
cd ~/.me
git add .
git commit -m "Update configs"
git push

# On machine 2
cd ~/.me
git pull
./itsame  # Re-apply symlinks if needed
```

## Profile Switching

Try different configurations by switching profiles:

```bash
# Try a friend's profile
git clone git@github.com:mario/dotme.git ~/.me.mario
./itsame mario              # Switches to Mario's setup

# Switch back to your profile
cd ~/.me
./itsame                    # Back to your main profile

# Clone and switch in one command
./itsame mario git@github.com:mario/dotme.git
```

### How Switching Works
- Validates target profile exists and has required structure
- Creates new symlinks from target profile (`ln -sf` overwrites existing)
- Warns if tools are missing but continues (doesn't fail)
- On failure: run `./itsame` again or `./itsame other_profile` (idempotent)

### Multi-Profile Structure

When working with multiple profiles, your home directory layout looks like this:

```
~/
├── .me/                          # Main profile (version controlled)
│   ├── plugins/                  # Self-contained tool scripts
│   ├── claude/                   # Claude preferences
│   ├── config/lvim/              # LunarVim config
│   ├── iterm/                    # iTerm config
│   ├── itsame                    # Orchestrator
│   └── rollback                  # Restore script
│
├── .me.mario/                    # Friend's profile (optional)
│   ├── plugins/
│   ├── claude/
│   └── ...
│
├── .me.work/                     # Work profile (corporate tools)
│   ├── plugins/                  # IntelliJ, Jira, Slack, AWS CLI
│   ├── config/intellij/          # IntelliJ IDEA settings
│   ├── aws/                      # AWS CLI profiles
│   ├── vpn/                      # Corporate VPN configs
│   └── git/                      # Work git config (corporate email)
│
└── .me.bkp/                      # Original backup
    ├── claude/                   # Original ~/.claude/* items
    ├── config/lvim/              # Original ~/.config/lvim
    └── iterm/                    # Original ~/.iterm
```

## Adding New Tools

1. Create plugin script:
```bash
cat > plugins/cursor.sh <<'EOF'
#!/usr/bin/env bash
set -euo pipefail

if ! command -v cursor &> /dev/null; then
    echo "📥 Cursor not installed. Install via: brew install --cask cursor"
    exit 0
fi

if [[ -d "$HOME/.config/Cursor" ]] && [[ ! -L "$HOME/.config/Cursor" ]] && [[ ! -d "$BACKUP_DIR/config/Cursor" ]]; then
    echo "📦 Backing up Cursor config"
    mkdir -p "$BACKUP_DIR/config"
    mv "$HOME/.config/Cursor" "$BACKUP_DIR/config/Cursor"
fi

mkdir -p "$HOME/.config/Cursor/User"
ln -sf "$PROFILE_DIR/cursor/settings.json" "$HOME/.config/Cursor/User/settings.json"
echo "✓ Cursor configured"
EOF
chmod +x plugins/cursor.sh
```

2. Run itsame to activate:
```bash
./itsame
```

## Rollback

Restore your original pre-.me state:

```bash
./rollback  # Restores configs from ~/.me.bkp/
```

This removes all symlinks and restores your original configuration files.

## Key Features

### 1. Plugin-Based
- Self-contained scripts for each tool
- Auto-discovery from `plugins/` directory
- Easy to add new tools without modifying orchestrator

### 2. Idempotent
Run `./itsame` anytime. Safe to run multiple times.

### 3. Backup First
- One-time backup of original configs to `~/.me.bkp/`
- Preserved across re-runs (won't overwrite backups)
- Rollback script to restore anytime

### 4. Profile Switching
- Try different configurations without losing yours
- Switch between profiles with one command
- Each profile is independent

### 5. Graceful Degradation
- Plugins warn if tools not installed (don't fail)
- Missing files in profile logged but don't stop switching
- Best-effort approach for flexibility

## Troubleshooting

### Broken Symlinks
```bash
cd ~/.me
./itsame  # Re-runs idempotently, fixes links
```

### Restore from Backup
```bash
./rollback  # Restores everything from ~/.me.bkp/
```

### Check Symlinks
```bash
ls -la ~/.claude | grep "^l"     # List Claude symlinks
ls -la ~/.config | grep "^l"     # List config symlinks
```

## Future Extensions

Potential features:

**Top Priority:**
- Profile metadata (description, author, version) in `.me.yaml`

**Near-term:**
- `./addtool cursor ~/.cursor` - Generate plugin template automatically
- `letsago` - List available profiles (e.g., `ls -al ~/.me*`)
- `itsame --diff mario` - Show differences between profiles

**Advanced:**
- `itsatree` - Visual tree representation of profile structure
- Matrix comparison table showing which plugins are configured in each profile:
  ```
  | Plugin | me     | mario  |
  |--------|--------|--------|
  | cursor | config | -      |
  | claude | skills | prompts|
  ```

## License

Your configuration, your rules. Fork and customize freely.
