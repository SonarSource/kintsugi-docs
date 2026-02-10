# Kintsugi

An Agentic Development Environment for Claude Code that provides visual task management and code quality analysis.

## Getting Started

### Requirements

**Required:**
- **Node.js 22+** and **npm 10+**
- **Claude Code CLI** - Active subscription and installed CLI
- **Git** - For repository management

**Recommended:**
- **Java 17+** - For SonarQube for IDE code analysis
- **jq** - JSON processing tool for CLI hooks

### What is Kintsugi?

Kintsugi is a desktop application that extends Claude Code with visual task management and automated code quality checks. It works by installing CLI hooks that interact with a local database to track your development workflow.

**Key capabilities:**
- Automatic task tracking as you work with Claude Code
- Visual Kanban board for managing development workflow
- Code quality analysis with SonarQube for IDE integration
- File diff generation and review interface

**Note:** The plugin adds approximately 2% to token costs due to hook processing overhead.

### Installation

=== "macOS"
    Download the `.dmg` installer, drag to Applications, and launch.

=== "Windows"
    Download the `.exe` installer and follow the installation wizard.

=== "Linux"
    Download the `.AppImage`:
    ```bash
    chmod +x Kintsugi-x.y.z.AppImage
    ./Kintsugi-x.y.z.AppImage
    ```

### Setup

On first launch, Kintsugi will guide you through:

1. **CLI hooks installation** - Registers hooks in `~/.claude/settings.json` to capture Claude Code events
2. **Repository selection** - Choose a Git repository to track
3. **Optional integrations** - Configure SonarQube, JIRA, or GitHub (can be skipped)

The setup process creates:
- Local backend server on port 63421
- SQLite database at `~/.kintsugi/local.db`
- Configuration files in `~/.kintsugi/`

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
node --version  # Should be 22+
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

Check if port 8765 is available:
```bash
lsof -i :8765
```

If Java is not found, install it:
- macOS: `brew install openjdk@17`
- Linux: `apt-get install openjdk-17-jdk`
- Windows: Download from Oracle or OpenJDK

**Analysis fails or times out**

Increase analysis timeout in Settings → Code Analysis → Analysis Timeout (default is 120s, try 300s).

Check available system resources. Large files may require more memory.

Restart SonarLint bridge from Settings → SonarQube for IDE → Restart Bridge.

### Application Issues

**Need to restart after adding Java**

If you install Java while Kintsugi is running, the application won't detect it automatically. Fully quit and restart Kintsugi to enable SonarQube for IDE features.

**Hooks not firing / Tasks not appearing**

Verify hooks are registered:
```bash
cat ~/.claude/settings.json | jq .hooks
```

Re-register hooks if needed:
```bash
kintsugi unregister-hooks
kintsugi register-hooks
```

Check hook logs:
```bash
tail -f ~/.kintsugi/hook-debug.log
```

Ensure backend is running:
```bash
curl http://localhost:63421/health
```

**High memory usage**

Archive old completed tasks (Settings → Tasks → Archive).

Reduce terminal scrollback: Settings → Terminal → Scrollback Lines.

Vacuum database periodically:
```bash
sqlite3 ~/.kintsugi/local.db "VACUUM"
```

**Desktop app won't launch**

Clear cache:
```bash
rm -rf ~/.kintsugi/cache/
```

Reset settings (creates backup):
```bash
mv ~/.kintsugi/desktop-config.json ~/.kintsugi/desktop-config.json.backup
```

**Terminal not working**

Try a different shell in Settings → Terminal → Shell (bash vs zsh).

Reset terminal settings: Settings → Terminal → Reset Defaults.

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
- Application: Check DevTools (Cmd/Ctrl+Shift+I) Console tab

**Report issues:**

Open an issue on GitHub with:
- Problem description and steps to reproduce
- Version information
- Relevant log excerpts (sanitize sensitive data)
- Screenshots if applicable
