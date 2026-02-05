# Configuration Guide

This guide covers all configuration options available in Kintsugi, from basic settings to advanced customization.

## Configuration Files

Kintsugi uses several configuration files stored in `~/.kintsugi/`:

| File | Purpose | Format |
|------|---------|--------|
| `config.json` | Backend connection and auth | JSON |
| `desktop-config.json` | UI preferences and credentials | JSON |
| `local.db` | SQLite database | Binary |
| `local-backend.log` | Backend server logs | Text |
| `hook-debug.log` | CLI plugin logs | Text |

Additionally, Claude hooks are registered in `~/.claude/settings.json`.

## Application Settings

Access settings through: Settings button (gear icon) or `Ctrl/Cmd + ,`

### General Settings

**Appearance**
- **Theme**: Light, Dark, or System (follows OS theme)
- **Color Scheme**: Primary accent color
- **Font Size**: UI font size (Small, Medium, Large)
- **Compact Mode**: Reduce spacing for more content

**Behavior**
- **Start on Login**: Launch Kintsugi when you log in
- **Minimize to Tray**: Keep running in system tray when closed
- **Confirm Before Quit**: Show confirmation dialog
- **Auto-Update**: Automatically check for updates

**Notifications**
- **Desktop Notifications**: Enable system notifications
- **Sound**: Play sound for notifications
- **Notification Types**:
  - Task status changes
  - Approval requests
  - SonarLint issues found
  - Token budget alerts

### Terminal Settings

**Shell Configuration**
- **Default Shell**: Choose from detected shells
  - bash
  - zsh
  - fish
  - PowerShell (Windows)
  - Command Prompt (Windows)
- **Custom Shell**: Specify path to custom shell
- **Shell Arguments**: Additional arguments to pass

**Appearance**
- **Font Family**: Monospace font for terminal
  - Cascadia Code
  - Fira Code
  - JetBrains Mono
  - Menlo
  - Monaco
  - Courier New
  - Custom
- **Font Size**: 10-20px (default: 14px)
- **Line Height**: 1.0-2.0 (default: 1.2)
- **Cursor Style**: Block, Line, or Underline
- **Cursor Blink**: Enable cursor blinking

**Behavior**
- **Scrollback Lines**: History buffer size (1000-100000)
- **Scroll Sensitivity**: Mouse wheel scroll speed
- **Copy on Select**: Auto-copy selection to clipboard
- **Paste on Right Click**: Paste with right-click
- **Confirm Before Clear**: Prompt when clearing terminal

**Theme**
- **Terminal Theme**: Independent from app theme
  - Light
  - Dark
  - Custom (define colors)

### Task Management

**Auto-Creation**
- **Create on Prompt**: Auto-create task on prompt submit (recommended)
- **Require Confirmation**: Show modal before creating
- **Default Status**: Initial status for new tasks

**Status Transitions**
- **Auto-Progress**: Automatically move to "In Progress" on first edit
- **Auto-Review**: Move to "Ready for Review" on session end
- **Auto-Complete**: Never auto-complete (always manual)

**Display**
- **Card Density**: Compact, Normal, or Expanded
- **Show Token Cost**: Display cost badge on cards
- **Show Timestamps**: Show relative time on cards
- **Truncate Prompts**: Max length for prompt text
- **Group by Epic**: Show epics as groups

**Filters**
- **Default View**: All tasks or filtered view
- **Persist Filters**: Remember filter selections
- **Quick Filters**: Customize quick filter buttons

### Code Analysis

**SonarLint**
- **Enable Analysis**: Turn on/off SonarLint integration
- **Auto-Analyze**: Analyze on every file change
- **Severity Threshold**: Minimum severity to display
  - All
  - Info and above
  - Minor and above
  - Major and above
  - Critical and above
  - Blocker only
- **Show in Diff**: Display issues inline in diff viewer
- **Issue Notifications**: Desktop notification for new issues

**Languages**
Select which languages to analyze:
- Java, JavaScript, TypeScript
- Python, C#, C, C++
- PHP, Kotlin, Ruby, Scala
- Go, Swift, HTML, CSS
- XML, YAML, JSON
- All others (25+ total)

**Performance**
- **Analysis Timeout**: Max time for analysis (30-300s)
- **Max File Size**: Skip files larger than threshold (1-10MB)
- **Parallel Analysis**: Analyze multiple files concurrently

### Diff Viewer

**Display**
- **View Mode**: Side-by-side or Unified
- **Syntax Highlighting**: Enable code coloring
- **Line Numbers**: Show line numbers
- **Whitespace**: Show/hide whitespace changes
- **Context Lines**: Lines of context (0-10)

**Navigation**
- **Wrap Lines**: Word wrap long lines
- **Scroll Sync**: Sync scrolling between sides
- **Jump to Change**: Keyboard shortcut to next change

### Keyboard Shortcuts

**View Shortcuts**
- List all keyboard shortcuts
- Organized by category

**Customize**
- Click on shortcut to rebind
- Press new key combination
- Save or cancel

**Categories**
- Global
- Terminal
- Task Management
- Diff Viewer
- Code Editor

**Reset**
- Reset individual shortcut
- Reset category
- Reset all to defaults

### Advanced Settings

**Backend**
- **Backend URL**: Local backend address (default: `http://localhost:63421`)
- **Auto-Start**: Start backend with app
- **Port**: Backend server port (default: 63421)
- **Log Level**: Error, Warn, Info, Debug

**SonarLint Bridge**
- **Auto-Start**: Start bridge with app
- **Port**: Bridge server port (default: 8765)
- **Java Path**: Path to Java executable (auto-detected)
- **JVM Options**: Additional JVM arguments
- **Analyzer Cache**: Cache directory for analyzers

**Database**
- **Database Path**: SQLite database location
- **Backup Frequency**: Automatic backup interval
- **Backup Location**: Where to store backups
- **Max Backups**: Number of backups to keep

**Privacy**
- **Telemetry**: Enable/disable telemetry
- **Error Reporting**: Send crash reports (Sentry)
- **Device ID**: Unique anonymous identifier

**Developer**
- **Debug Mode**: Enable debug logging
- **Dev Tools**: Open Chrome DevTools
- **Reload**: Reload application
- **Clear Cache**: Clear application cache

## Integration Configuration

### SonarQube Setup

**Connection**
1. **Open Settings → SonarQube**
2. **Enter Server URL**
   - Example: `https://sonarqube.company.com`
3. **Authentication Method**
   - **Token**: Personal access token (recommended)
   - **User/Pass**: Username and password
4. **Test Connection**
   - Click "Test" to verify
5. **Save**

**Project Binding**
1. **Select Repository**
2. **Click "Bind to SonarQube Project"**
3. **Choose Organization** (if using SonarCloud)
4. **Select Project** from dropdown
5. **Sync Quality Profile**
   - Choose profile to use
   - Download rules
6. **Save Binding**

**Quality Profile Sync**
- **Auto-Sync**: Sync on app startup
- **Sync Frequency**: Manual or scheduled
- **Last Sync**: Shows last sync time
- **Sync Now**: Manual trigger

### JIRA Configuration

**Credentials**
1. **Open Settings → JIRA**
2. **Enter JIRA URL**
   - Example: `https://yourcompany.atlassian.net`
3. **Enter Email**
   - Your JIRA account email
4. **Create API Token**
   - Go to Atlassian Account Settings
   - Security → API Tokens
   - Create token
   - Copy token
5. **Enter Token** in Kintsugi
6. **Test Connection**
7. **Save**

**Project Mapping**
- **Default Project**: Auto-link to this project
- **Custom Mappings**: Map repos to JIRA projects

**Sync Settings**
- **Auto-Fetch**: Fetch ticket data automatically
- **Cache Duration**: How long to cache ticket data (5-60 min)
- **Fetch Comments**: Include ticket comments

### GitHub Configuration

**Authentication**
1. **Open Settings → GitHub**
2. **Generate Personal Access Token**
   - Go to GitHub → Settings → Developer settings → Personal access tokens
   - Generate new token (classic)
   - Scopes needed:
     - `repo` (full access)
     - `read:org` (read organization data)
3. **Enter Token** in Kintsugi
4. **Test Connection**
5. **Save**

**Repository Mapping**
- **Auto-Detect**: Use git remote URL
- **Manual Override**: Specify owner/repo

**Pull Request Settings**
- **Auto-Detect**: Detect PR references in prompts
- **PR Actions**: What to do when PR detected
  - Create card
  - Link to task
  - Ignore

## CLI Plugin Configuration

The CLI plugin is configured automatically, but can be customized.

### Hook Registration

Location: `~/.claude/settings.json`

```json
{
  "hooks": {
    "UserPromptSubmit": "/path/to/kintsugi/hooks/on-prompt-submit.js",
    "PreToolUse": "/path/to/kintsugi/hooks/on-tool-use.js",
    "PostToolUse": "/path/to/kintsugi/hooks/on-post-tool-use.js",
    "Stop": "/path/to/kintsugi/hooks/on-stop.js",
    "SessionStart": "/path/to/kintsugi/hooks/on-session-start.js",
    "SessionEnd": "/path/to/kintsugi/hooks/on-session-end.js",
    "Notification": "/path/to/kintsugi/hooks/on-notification.js",
    "PermissionRequest": "/path/to/kintsugi/hooks/on-permission-request.js"
  }
}
```

### Plugin Settings

Location: `~/.kintsugi/config.json`

```json
{
  "backendUrl": "http://localhost:63421",
  "authToken": "auto-generated-token",
  "logLevel": "info",
  "snapshotDir": "/Users/you/.kintsugi/task-snapshots"
}
```

### Manual Hook Registration

If automatic registration fails:

```bash
kintsugi register-hooks
```

Or manually:
```bash
kintsugi register-hooks --claude-settings ~/.claude/settings.json
```

### Unregister Hooks

To remove hooks:

```bash
kintsugi unregister-hooks
```

## Environment Variables

Override configuration with environment variables.

### Backend Variables

```bash
KINTSUGI_PORT=63421
KINTSUGI_LOG_LEVEL=info
KINTSUGI_DB_PATH=~/.kintsugi/local.db
KINTSUGI_DISABLE_TELEMETRY=true
```

### SonarLint Bridge Variables

```bash
SONARLINT_BRIDGE_PORT=8765
SONARLINT_JAVA_PATH=/path/to/java
SONARLINT_JVM_OPTS=-Xmx2g
```

### Plugin Variables

```bash
KINTSUGI_BACKEND_URL=http://localhost:63421
KINTSUGI_LOG_LEVEL=debug
KINTSUGI_SNAPSHOT_DIR=~/.kintsugi/task-snapshots
```

## Approval System Configuration

Customize which operations require approval.

### Tool Approval Rules

**Settings → Approvals → Tool Rules**

For each tool, configure:
- **Require Approval**: Yes/No
- **Timeout**: How long to wait (seconds)
- **Auto-Allow Patterns**: Paths/patterns that bypass approval
- **Auto-Deny Patterns**: Paths/patterns that always deny

### Example Configuration

**Write Tool**
- Require Approval: Yes
- Timeout: 120s
- Auto-Allow:
  - `*.test.js`
  - `*.spec.ts`
  - `docs/**/*`
- Auto-Deny:
  - `.env`
  - `config/production.*`
  - `*.key`

**Bash Tool**
- Require Approval: Yes
- Timeout: 60s
- Auto-Allow:
  - `npm test`
  - `npm run build`
  - `git status`
- Auto-Deny:
  - `rm -rf`
  - `dd if=`
  - `:(){ :|:& };:`

### Risk Assessment

Configure automatic risk scoring:

**File Patterns**
- **High Risk**: Production configs, credentials, scripts
- **Medium Risk**: Source code, package manifests
- **Low Risk**: Tests, docs, assets

**Commands**
- **High Risk**: Destructive operations, system changes
- **Medium Risk**: Build commands, git operations
- **Low Risk**: Read-only commands, status checks

## Import/Export Configuration

### Export Settings

1. **Settings → Advanced → Export**
2. **Choose Components**
   - Application settings
   - Integration credentials
   - Keyboard shortcuts
   - Approval rules
3. **Export to File**
   - Saves JSON file
4. **Share or Backup**

### Import Settings

1. **Settings → Advanced → Import**
2. **Select File**
3. **Choose Components** to import
4. **Confirm**
   - Optionally backup current settings first

### Team Configuration Templates

Create templates for team:
1. **Configure ideal settings**
2. **Export configuration**
3. **Share file with team**
4. **Team members import**
5. **Consistent setup across team**

## Troubleshooting Configuration

### Reset to Defaults

**Individual Settings**
- Click "Reset" next to setting
- Confirms before resetting

**All Settings**
- Settings → Advanced → Reset All
- Backup prompt shown
- Restarts application

### Configuration Validation

**Settings → Advanced → Validate**
- Checks all configuration files
- Verifies backend connectivity
- Tests integration credentials
- Reports issues found

### Log Files

Check logs for configuration issues:

```bash
tail -f ~/.kintsugi/local-backend.log
tail -f ~/.kintsugi/hook-debug.log
```

### Common Issues

**Hooks Not Working**
- Verify `~/.claude/settings.json` has hook entries
- Check hook script permissions: `chmod +x hooks/*.js`
- Re-register: `kintsugi register-hooks`

**Backend Won't Start**
- Check port not in use: `lsof -i :63421`
- Verify Node.js version: `node --version` (need 22+)
- Check logs: `~/.kintsugi/local-backend.log`

**SonarLint Not Analyzing**
- Verify Java installed: `java --version` (need 17+)
- Check bridge running: `lsof -i :8765`
- Review bridge logs in app

## Next Steps

- **[Features Overview](features.md)** - Learn what you can configure
- **[Integrations Guide](integrations.md)** - Detailed integration setup
- **[Troubleshooting](troubleshooting.md)** - Fix configuration issues
- **[Workflows](workflows.md)** - Use configured features
