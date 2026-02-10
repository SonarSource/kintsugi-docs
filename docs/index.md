# Kintsugi

An Agentic Development Environment for Claude Code that provides visual task management and code quality analysis.

## Getting Started

### Requirements

**Required:**

- **Node.js 20+** and **npm 10+** - [Download Node.js](https://nodejs.org/)
- **Claude Code CLI** - Active subscription and installed CLI - [Get Claude Code](https://claude.ai/download)
- **Git** - For repository management - [Download Git](https://git-scm.com/downloads)
- **Java 17+** - Required for SonarQube for IDE code analysis - [Download OpenJDK](https://openjdk.org/) or [Oracle JDK](https://www.oracle.com/java/technologies/downloads/)

!!! warning "Platform Support"
    Currently only **macOS** is supported. Windows and Linux support coming soon.

!!! warning "x64 Architecture Limitation"
    On x64 (Intel) Macs, SonarQube Context Augmentation features will not work. Full functionality requires Apple Silicon (ARM64).

### What is Kintsugi?

Kintsugi is a desktop application that extends Claude Code with visual task management and automated code quality checks. It works by installing CLI hooks that interact with a local database to track your development workflow.

**Key capabilities:**

- Automatic task tracking as you work with Claude Code
- Visual Kanban board for managing development workflow
- Code quality analysis with SonarQube for IDE integration
- File diff generation and review interface

**Note:** The plugin adds approximately 2% to token costs due to hook processing overhead.

### Installation

Download the `.dmg` installer for macOS, drag to Applications, and launch.

### Setup

On first launch, Kintsugi will guide you through:

1. **CLI hooks installation** - Registers hooks in `~/.claude/settings.json` to capture Claude Code events
2. **Repository folder selection** - Select the folder where your Git repositories are located
3. **Optional integrations** - Configure SonarQube or JIRA (can be skipped)

The setup process creates:

- Local backend server on port 63421
- SQLite database at `~/.kintsugi/local.db`
- Configuration files in `~/.kintsugi/`

**Manual plugin installation:**

If the automatic installation fails, you can install the plugin manually using Claude Code:

```bash
claude
/plugin marketplace add /Applications/Kintsugi.app/Contents/Resources/kintsugi-marketplace
/plugin install kintsugi@kintsugi-marketplace
/exit
```

### First Session

1. Open terminal in Kintsugi or use your system terminal
2. Navigate to your project
3. Run Claude Code: `claude "your task description"`
4. Watch tasks appear automatically on the Kanban board

## FAQ/Troubleshooting

### Local Backend Issues

**Backend won't start**

Check if port is already in use:
```bash
lsof -i :63421
```

If occupied, kill the process:
```bash
kill -9 <PID>
```

Verify Node.js version:
```bash
node --version  # Should be 20+
```

Check backend logs:
```bash
tail -f ~/.kintsugi/local-backend.log
```

**Backend crashes repeatedly**

Check available disk space and memory. The backend requires at least 256MB RAM.

Verify database integrity:
```bash
sqlite3 ~/.kintsugi/local.db "PRAGMA integrity_check"
```

If corrupted, backup and recreate:
```bash
cp ~/.kintsugi/local.db ~/.kintsugi/local.db.backup
rm ~/.kintsugi/local.db
```

Restart Kintsugi to create a fresh database.

### SonarQube for IDE Issues

**SonarLint bridge won't start**

Verify Java installation:
```bash
java --version  # Should be 17+
```

If Java is not found, install it:

- macOS: `brew install openjdk`

Download from [OpenJDK](https://openjdk.org/) or [Oracle JDK](https://www.oracle.com/java/technologies/downloads/)

**Analysis fails**

Check available system resources. Large files may require more memory.

Restart the Kintsugi application to reinitialize the SonarLint bridge.

### Application Issues

**Need to restart after adding Java**

If you install Java while Kintsugi is running, the application won't detect it automatically. Fully quit and restart Kintsugi to enable SonarQube for IDE features.

**Hooks not firing / Tasks not appearing**

Verify hooks are registered using Claude Code:
```bash
claude /plugin
```

Re-install plugin if needed:
```bash
claude
/plugin marketplace add /Applications/Kintsugi.app/Contents/Resources/kintsugi-marketplace
/plugin install kintsugi@kintsugi-marketplace
/exit
```

Ensure backend is running:
```bash
curl http://localhost:63421/health
```

**Desktop app won't launch**

Try deleting the SQLite database (this will remove all tasks):
```bash
rm ~/.kintsugi/local.db
```

Restart Kintsugi to create a fresh database.

**Terminal not working**

Close and reopen the terminal panel. If issues persist, restart the Kintsugi application.

**Diffs not generating**

Verify snapshots are being created:
```bash
ls -la ~/.kintsugi/task-snapshots/tasks/<task-id>/
```

Check that both `.before` and `.after` files exist for modified files.

Re-register hooks if PreToolUse hook is not capturing snapshots.

### Getting Help

**Collect diagnostic information:**

```bash
kintsugi --version
node --version
java --version
claude --version
```

**Review logs:**

- Backend: `~/.kintsugi/local-backend.log`
- Hooks: `~/.kintsugi/hook-debug.log`
- Application: Check DevTools (Cmd+Shift+I) Console tab

**Report issues:**

Open an issue on GitHub with:
- Problem description and steps to reproduce
- Version information
- Relevant log excerpts (sanitize sensitive data)
- Screenshots if applicable
