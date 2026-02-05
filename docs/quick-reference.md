# Quick Reference

Quick answers to common questions and tasks. For detailed information, see the full documentation.

## Installation

```bash
# macOS
# Download .dmg, drag to Applications

# Linux (AppImage)
chmod +x Kintsugi-x.y.z.AppImage
./Kintsugi-x.y.z.AppImage

# Install plugin
npm install -g @kintsugi/plugin
kintsugi register-hooks
```

## First-Time Setup

1. Launch Kintsugi
2. Follow onboarding wizard
3. Select your project repository
4. Open terminal in Kintsugi
5. Start Claude: `claude "your prompt"`

## Common Commands

### CLI Plugin

```bash
kintsugi --version              # Check version
kintsugi register-hooks         # Register hooks
kintsugi unregister-hooks       # Remove hooks
kintsugi generate-diff --task-id <id>  # Generate diff manually
kintsugi find-repos            # Scan for repositories
```

### Claude Usage

```bash
claude "Add error handling to API"  # Submit prompt
claude                              # Interactive mode
```

### Backend Health

```bash
curl http://localhost:63421/health   # Check backend
lsof -i :63421                      # Backend running?
lsof -i :8765                       # SonarLint running?
```

## File Locations

```bash
~/.kintsugi/local.db              # Database
~/.kintsugi/config.json           # Configuration
~/.kintsugi/desktop-config.json   # UI settings
~/.kintsugi/task-snapshots/       # File snapshots
~/.kintsugi/local-backend.log     # Backend logs
~/.kintsugi/hook-debug.log        # Hook logs
~/.claude/settings.json           # Hook registration
~/.claude/logs/                   # Claude transcripts
```

## Keyboard Shortcuts

```
Ctrl/Cmd + ,         Open Settings
Ctrl/Cmd + `         Toggle Terminal
Ctrl/Cmd + K         Focus Search
Ctrl/Cmd + N         New Task
Ctrl/Cmd + E         New Epic
Ctrl/Cmd + T         New Terminal Tab
Enter                Open Selected Task
Delete               Delete Selected Task
Esc                  Close Modal
```

## Task Status Flow

```
To Do → In Progress → Awaiting Input → Ready for Review → Done
```

## Troubleshooting Quick Fixes

### Backend Won't Start

```bash
lsof -i :63421 | grep LISTEN
kill -9 <PID>
```

### Hooks Not Working

```bash
kintsugi unregister-hooks
kintsugi register-hooks
cat ~/.claude/settings.json | grep hooks
```

### Database Issues

```bash
# Backup
cp ~/.kintsugi/local.db ~/.kintsugi/local.db.backup

# Vacuum
sqlite3 ~/.kintsugi/local.db "VACUUM"

# Reset (deletes all data!)
rm ~/.kintsugi/local.db
```

### SonarLint Not Working

```bash
java --version                    # Check Java 17+
lsof -i :8765                    # Check bridge running
```

### Clear Cache

```bash
rm -rf ~/.kintsugi/cache/
rm -rf ~/.kintsugi/task-snapshots/
```

## Integration Setup

### SonarQube

1. Get token from SonarQube → My Account → Security
2. Settings → SonarQube
3. Enter URL and token
4. Test connection
5. Bind project

### JIRA

1. Get API token from [Atlassian Account](https://id.atlassian.com/)
2. Settings → JIRA
3. Enter URL, email, token
4. Test connection

### GitHub

1. Get token from [GitHub Settings](https://github.com/settings/tokens)
2. Scopes: `repo`, `read:org`
3. Settings → GitHub
4. Enter token
5. Test connection

## Configuration Examples

### Enable Auto-Analysis

Settings → Code Analysis → Auto-Analyze: On

### Change Backend Port

Settings → Advanced → Backend Port: 63421

### Disable Telemetry

Settings → Privacy → Telemetry: Off

Or:
```bash
export KINTSUGI_DISABLE_TELEMETRY=true
```

## Database Queries

```bash
# Count tasks
sqlite3 ~/.kintsugi/local.db "SELECT COUNT(*) FROM tasks"

# Recent tasks
sqlite3 ~/.kintsugi/local.db "SELECT prompt, status FROM tasks ORDER BY created_at DESC LIMIT 10"

# Tasks by status
sqlite3 ~/.kintsugi/local.db "SELECT status, COUNT(*) FROM tasks GROUP BY status"

# Total token cost
sqlite3 ~/.kintsugi/local.db "SELECT SUM(total_cost) FROM task_consumption"
```

## Backup & Restore

### Backup

```bash
# Full backup
tar -czf kintsugi-backup-$(date +%Y%m%d).tar.gz ~/.kintsugi/

# Database only
cp ~/.kintsugi/local.db ~/backups/local.db.$(date +%Y%m%d)

# Export tasks
# Settings → Data → Export Tasks
```

### Restore

```bash
# Full restore
tar -xzf kintsugi-backup-20240101.tar.gz -C ~/

# Database only
cp ~/backups/local.db.20240101 ~/.kintsugi/local.db
```

## Logs

### View Logs

```bash
# Backend logs
tail -f ~/.kintsugi/local-backend.log

# Hook logs
tail -f ~/.kintsugi/hook-debug.log

# Filter errors
grep ERROR ~/.kintsugi/local-backend.log

# Last 50 lines
tail -50 ~/.kintsugi/local-backend.log
```

### Clear Logs

```bash
> ~/.kintsugi/local-backend.log
> ~/.kintsugi/hook-debug.log
```

## Maintenance Tasks

### Weekly

```bash
# Archive old tasks
# Settings → Tasks → Archive Completed

# Clear terminal history
# Terminal → Clear

# Check disk space
du -sh ~/.kintsugi/
```

### Monthly

```bash
# Vacuum database
sqlite3 ~/.kintsugi/local.db "VACUUM"

# Clean old snapshots
find ~/.kintsugi/task-snapshots/ -mtime +30 -delete

# Update Kintsugi
# Check for updates in app
```

## Performance Tips

1. **Limit Active Tasks** - Archive tasks older than 30 days
2. **Reduce Scrollback** - Settings → Terminal → Scrollback: 1000
3. **Disable Unused Features** - Turn off SonarLint if not needed
4. **Regular Cleanup** - Vacuum database monthly
5. **Filter Views** - Use filters to reduce rendered items

## Security Tips

1. **Rotate Tokens** - Change integration tokens quarterly
2. **Review Credentials** - Audit Settings → Integrations
3. **Secure Machine** - Use disk encryption
4. **Check Snapshots** - Review for secrets periodically
5. **Clear Old Data** - Delete unnecessary snapshots

## Environment Variables

```bash
# Backend
export KINTSUGI_PORT=63421
export KINTSUGI_LOG_LEVEL=info
export KINTSUGI_DISABLE_TELEMETRY=true

# SonarLint
export SONARLINT_BRIDGE_PORT=8765
export SONARLINT_JAVA_PATH=/path/to/java

# Plugin
export KINTSUGI_BACKEND_URL=http://localhost:63421
export KINTSUGI_LOG_LEVEL=debug
```

## API Examples

### REST API

```bash
# Health check
curl http://localhost:63421/health

# Get tasks
curl -H "Authorization: Bearer <token>" \
  http://localhost:63421/api/tasks

# Create task
curl -X POST \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Add feature","status":"todo"}' \
  http://localhost:63421/api/tasks
```

## Getting Help

- **Documentation**: [Full Docs](../README.md)
- **Troubleshooting**: [Troubleshooting Guide](troubleshooting.md)
- **Issues**: [GitHub Issues](https://github.com/your-org/kintsugi/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/kintsugi/discussions)

## Version Check

```bash
# Kintsugi plugin
kintsugi --version

# Node.js (need 22+)
node --version

# Java (need 17+)
java --version

# Claude CLI
claude --version
```

## Full Documentation Links

- [Getting Started](getting-started.md)
- [Features Overview](features.md)
- [User Workflows](workflows.md)
- [Configuration](configuration.md)
- [Architecture](architecture.md)
- [Integrations](integrations.md)
- [Troubleshooting](troubleshooting.md)
- [Data & Privacy](data-privacy.md)
