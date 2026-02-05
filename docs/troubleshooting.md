# Troubleshooting Guide

This guide helps you diagnose and resolve common issues with Kintsugi. Issues are organized by category with step-by-step solutions.

## General Troubleshooting Steps

Before diving into specific issues, try these general steps:

1. **Check Logs**
   ```bash
   tail -f ~/.kintsugi/local-backend.log
   tail -f ~/.kintsugi/hook-debug.log
   ```

2. **Verify Services Running**
   ```bash
   lsof -i :63421  # Local backend
   lsof -i :8765   # SonarLint Bridge
   ```

3. **Restart Application**
   - Quit Kintsugi completely
   - Kill any stuck processes
   - Relaunch

4. **Check Versions**
   ```bash
   node --version    # Should be 22+
   java --version    # Should be 17+
   claude --version  # Verify Claude CLI installed
   kintsugi --version  # Verify plugin installed
   ```

## Installation Issues

### Application Won't Install

**macOS: "App can't be opened because it is from an unidentified developer"**

**Solution:**
1. Open System Preferences → Security & Privacy
2. Click "Open Anyway" next to the Kintsugi message
3. Or: Right-click app → Open → confirm

**Windows: SmartScreen Warning**

**Solution:**
1. Click "More info"
2. Click "Run anyway"
3. Or: Right-click installer → Properties → Unblock → OK

**Linux: Permission Denied**

**Solution:**
```bash
chmod +x Kintsugi-x.y.z.AppImage
```

### Plugin Installation Fails

**npm install fails**

**Symptoms:**
```bash
npm install -g @kintsugi/plugin
# Error: EACCES: permission denied
```

**Solution:**
```bash
sudo npm install -g @kintsugi/plugin
```

Or configure npm to install globally without sudo:
```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g @kintsugi/plugin
```

**Plugin not found**

**Symptoms:**
```bash
kintsugi --version
# command not found: kintsugi
```

**Solution:**

Check npm global path:
```bash
npm list -g --depth=0
npm root -g
```

Add to PATH:
```bash
echo 'export PATH="$(npm root -g)/../bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Backend Issues

### Backend Won't Start

**Port already in use**

**Symptoms:**
- Error: "Port 63421 is already in use"
- Backend fails to start

**Solution:**

Find process using port:
```bash
lsof -i :63421
```

Kill the process:
```bash
kill -9 <PID>
```

Or change port in settings.

**Node.js version too old**

**Symptoms:**
- Error: "Unsupported Node.js version"
- Backend crashes on start

**Solution:**

Check version:
```bash
node --version
```

Upgrade to Node.js 22+:
- macOS: `brew upgrade node`
- Linux: Use nvm or package manager
- Windows: Download from nodejs.org

**Database locked**

**Symptoms:**
- Error: "database is locked"
- Backend fails to start

**Solution:**

Close all Kintsugi instances:
```bash
pkill -f kintsugi
pkill -f local-backend
```

Remove lock file:
```bash
rm ~/.kintsugi/local.db-journal
```

Restart Kintsugi.

**Permissions issue**

**Symptoms:**
- Error: "EACCES: permission denied"
- Backend can't write to `~/.kintsugi/`

**Solution:**

Fix permissions:
```bash
chmod -R 755 ~/.kintsugi/
chown -R $USER ~/.kintsugi/
```

### Backend Crashes

**Out of memory**

**Symptoms:**
- Backend crashes randomly
- Error: "JavaScript heap out of memory"

**Solution:**

Increase memory limit:
```bash
export NODE_OPTIONS="--max-old-space-size=4096"
```

Add to shell config for permanent fix.

**SQLite corruption**

**Symptoms:**
- Backend crashes on startup
- Error: "database disk image is malformed"

**Solution:**

Backup and repair:
```bash
cp ~/.kintsugi/local.db ~/.kintsugi/local.db.backup
sqlite3 ~/.kintsugi/local.db "PRAGMA integrity_check"
```

If corrupted, restore from backup or recreate:
```bash
rm ~/.kintsugi/local.db
```

Restart Kintsugi (creates new database).

### Backend Performance Issues

**Slow queries**

**Solution:**

Check database size:
```bash
ls -lh ~/.kintsugi/local.db
```

If > 100MB, vacuum database:
```bash
sqlite3 ~/.kintsugi/local.db "VACUUM"
```

Clean old tasks:
- Delete completed tasks older than 30 days

**High CPU usage**

**Solution:**

Check consumption service:
- May be parsing large transcript files
- Check `~/.claude/logs/` size
- Archive old logs

Reduce polling frequency:
- Settings → Advanced → Consumption polling interval

## Hook Issues

### Hooks Not Firing

**Claude events not creating tasks**

**Symptoms:**
- Submit prompt to Claude
- No task appears in Kintsugi

**Diagnosis:**

Check hooks registered:
```bash
cat ~/.claude/settings.json | jq .hooks
```

Check hook logs:
```bash
tail -f ~/.kintsugi/hook-debug.log
```

**Solution:**

Re-register hooks:
```bash
kintsugi unregister-hooks
kintsugi register-hooks
```

Verify registration:
```bash
cat ~/.claude/settings.json
```

Restart Claude Code session.

**Hooks registered but not executing**

**Symptoms:**
- Hooks in settings.json
- But events not logged

**Diagnosis:**

Check hook script permissions:
```bash
ls -la $(npm root -g)/@kintsugi/plugin/dist/hooks/
```

**Solution:**

Fix permissions:
```bash
chmod +x $(npm root -g)/@kintsugi/plugin/dist/hooks/*.js
```

**Backend unreachable from hooks**

**Symptoms:**
- Hooks execute
- But fail to reach backend

**Diagnosis:**

Check backend health:
```bash
curl http://localhost:63421/health
```

Check hook logs for connection errors:
```bash
grep "ECONNREFUSED" ~/.kintsugi/hook-debug.log
```

**Solution:**

Ensure backend is running:
```bash
lsof -i :63421
```

Start backend if not running.

Check firewall not blocking localhost.

### Diff Generation Issues

**Diffs not appearing**

**Symptoms:**
- Task shows "View Diff" button disabled
- No diff generated when Claude stops

**Diagnosis:**

Check snapshots exist:
```bash
ls -la ~/.kintsugi/task-snapshots/tasks/<task-id>/
```

Check Stop hook logs:
```bash
grep "on-stop" ~/.kintsugi/hook-debug.log
```

**Solution:**

If no snapshots:
- PreToolUse hook may not be firing
- Check hook registration
- Verify file permissions

If snapshots exist but no diff:
- Manually generate:
  ```bash
  kintsugi generate-diff --task-id <task-id>
  ```

**Incorrect diffs**

**Symptoms:**
- Diff shows wrong changes
- Missing files or extra files

**Solution:**

Check snapshot timing:
- Ensure PreToolUse fires before edit
- Check PostToolUse fires after edit
- Verify Stop hook fires when Claude pauses

Clear snapshots and regenerate:
```bash
rm -rf ~/.kintsugi/task-snapshots/tasks/<task-id>/
```

Restart Claude session.

## SonarLint Issues

### Bridge Won't Start

**Java not found**

**Symptoms:**
- SonarLint features disabled
- Error: "Java not found"

**Solution:**

Install Java 17+:
- macOS: `brew install openjdk@17`
- Linux: `apt-get install openjdk-17-jdk`
- Windows: Download from Oracle or OpenJDK

Verify installation:
```bash
java --version
```

Set JAVA_HOME:
```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
```

**Port conflict**

**Symptoms:**
- Error: "Port 8765 already in use"

**Solution:**

Find conflicting process:
```bash
lsof -i :8765
kill -9 <PID>
```

Or change port in Settings → SonarLint → Bridge Port.

**Bridge crashes**

**Symptoms:**
- Analysis fails
- Bridge process disappears

**Solution:**

Check Java memory:
```bash
ps aux | grep sonarlint
```

Increase heap size in Settings:
- JVM Options: `-Xmx2g`

Check bridge logs in application logs.

### Analysis Issues

**Files not analyzed**

**Symptoms:**
- Click "Analyze" but nothing happens
- No issues detected

**Diagnosis:**

Check bridge running:
```bash
lsof -i :8765
```

Check file size:
```bash
ls -lh <file-path>
```

Check language supported:
- Settings → Code Analysis → Languages

**Solution:**

If bridge not running:
- Restart from Settings → SonarLint → Restart Bridge

If file too large:
- Increase limit in Settings → Code Analysis → Max File Size

If language not supported:
- Enable language in settings

**Analysis timeout**

**Symptoms:**
- Analysis takes forever
- Eventually fails with timeout

**Solution:**

Increase timeout:
- Settings → Code Analysis → Analysis Timeout
- Increase from 120s to 300s

Reduce file size:
- Large files may need splitting

Check system resources:
- High CPU/memory usage may slow analysis

### SonarQube Connected Mode Issues

**Cannot connect to SonarQube**

**Symptoms:**
- Test connection fails
- "Unable to reach server"

**Solution:**

Check URL:
- Verify format: `https://sonarqube.company.com`
- No trailing slash

Check network:
```bash
curl -I https://sonarqube.company.com
```

Check VPN if required.

Verify token:
- Regenerate in SonarQube
- Update in Kintsugi

**Quality profile sync fails**

**Symptoms:**
- Sync button fails
- Error in logs

**Solution:**

Check SonarQube version:
- Requires 9.9+

Check project exists:
- Verify in SonarQube UI

Check token permissions:
- Needs "Browse" permission

Try manual sync:
- Settings → SonarQube → Sync Now

## Integration Issues

### JIRA Integration Issues

**Cannot connect to JIRA**

**Symptoms:**
- Test connection fails
- Authentication error

**Solution:**

Verify URL format:
- Cloud: `https://yourcompany.atlassian.net`
- No `/jira` suffix

Check credentials:
- Email must match JIRA account
- API token valid

Regenerate token:
- Atlassian Account → Security → API Tokens
- Create new token
- Update in Kintsugi

**Tickets not loading**

**Symptoms:**
- Linked ticket shows "Loading..."
- Never resolves

**Solution:**

Check permissions:
- Verify you can view ticket in JIRA
- Check project access

Clear cache:
- Settings → JIRA → Clear Cache

Check network:
```bash
curl -u email:token https://yourcompany.atlassian.net/rest/api/3/issue/PROJ-123
```

### GitHub Integration Issues

**Token authentication fails**

**Symptoms:**
- Test connection fails
- "Bad credentials"

**Solution:**

Check token:
- Copy/paste carefully (no spaces)
- Verify not expired

Check scopes:
- Must have `repo` scope
- Add `read:org` for org repos

Regenerate token:
- GitHub Settings → Developer settings → Tokens
- Create new with required scopes

**Repository not detected**

**Symptoms:**
- Repository doesn't appear
- GitHub features unavailable

**Solution:**

Check git remote:
```bash
git remote -v
```

Add remote if missing:
```bash
git remote add origin https://github.com/user/repo.git
```

Verify URL format:
- HTTPS: `https://github.com/user/repo.git`
- SSH: `git@github.com:user/repo.git`

Refresh repository list:
- Settings → Repositories → Refresh

## UI Issues

### Application Won't Launch

**Blank white screen**

**Solution:**

Clear cache:
```bash
rm -rf ~/.kintsugi/cache/
```

Reset settings:
```bash
mv ~/.kintsugi/desktop-config.json ~/.kintsugi/desktop-config.json.backup
```

Restart application.

**Crashes on startup**

**Solution:**

Check logs:
```bash
# macOS
~/Library/Logs/Kintsugi/
# Linux
~/.config/Kintsugi/logs/
# Windows
%APPDATA%\Kintsugi\logs\
```

Try safe mode:
```bash
kintsugi --safe-mode
```

Reinstall if needed.

### Terminal Issues

**Terminal not working**

**Symptoms:**
- Terminal blank
- Commands don't execute
- No output shown

**Solution:**

Check shell config:
- Settings → Terminal → Shell
- Try different shell (bash vs zsh)

Check shell exists:
```bash
which bash
which zsh
```

Reset terminal settings:
- Settings → Terminal → Reset Defaults

**Terminal performance issues**

**Symptoms:**
- Slow typing
- Laggy scrolling
- High memory usage

**Solution:**

Reduce scrollback:
- Settings → Terminal → Scrollback Lines
- Reduce from 10000 to 1000

Clear terminal:
- Ctrl+L or type `clear`

Reduce font size:
- Settings → Terminal → Font Size

### Kanban Board Issues

**Tasks not appearing**

**Solution:**

Check filters:
- Clear all filters
- Reset view to default

Refresh board:
- Pull to refresh or
- Restart application

Check database:
```bash
sqlite3 ~/.kintsugi/local.db "SELECT COUNT(*) FROM tasks"
```

**Drag and drop not working**

**Solution:**

Restart application.

Check for modal overlays that may block interaction.

Try keyboard navigation:
- Select task with arrow keys
- Press Enter to open

**Performance issues with many tasks**

**Solution:**

Archive old tasks:
- Completed tasks older than 30 days

Use filters:
- Filter by branch or date range

Enable pagination:
- Settings → Display → Card Pagination

## Performance Issues

### High Memory Usage

**Symptoms:**
- Application uses > 1GB RAM
- System slowdown

**Solution:**

Check number of tasks:
```bash
sqlite3 ~/.kintsugi/local.db "SELECT COUNT(*) FROM tasks"
```

Clean up:
- Delete old tasks
- Archive completed tasks

Reduce terminal scrollback:
- Settings → Terminal → Scrollback Lines

Restart application regularly.

### High CPU Usage

**Symptoms:**
- Application uses > 50% CPU
- Fan noise, battery drain

**Solution:**

Check background processes:
```bash
ps aux | grep kintsugi
ps aux | grep sonarlint
```

Reduce polling:
- Settings → Advanced → Polling intervals

Disable unused features:
- SonarLint if not needed
- Telemetry

Restart application.

## Data Issues

### Lost Tasks

**Symptoms:**
- Tasks disappeared
- Board is empty

**Solution:**

Check database:
```bash
sqlite3 ~/.kintsugi/local.db "SELECT * FROM tasks LIMIT 10"
```

Check filters:
- Clear all filters
- Check date range

Restore from backup:
```bash
cp ~/.kintsugi/backups/local.db.backup ~/.kintsugi/local.db
```

### Database Corruption

**Symptoms:**
- Application crashes
- Error: "database malformed"

**Solution:**

Check integrity:
```bash
sqlite3 ~/.kintsugi/local.db "PRAGMA integrity_check"
```

Backup current:
```bash
cp ~/.kintsugi/local.db ~/.kintsugi/local.db.corrupted
```

Restore from backup:
```bash
cp ~/.kintsugi/backups/local.db.backup ~/.kintsugi/local.db
```

Or recreate:
```bash
rm ~/.kintsugi/local.db
```

Restart (creates fresh database).

## Getting Help

If you can't resolve your issue:

### Collect Information

1. **Version information:**
   ```bash
   kintsugi --version
   node --version
   java --version
   claude --version
   ```

2. **Logs:**
   ```bash
   cat ~/.kintsugi/local-backend.log > backend.log
   cat ~/.kintsugi/hook-debug.log > hooks.log
   ```

3. **Configuration:**
   ```bash
   cat ~/.claude/settings.json > claude-settings.json
   cat ~/.kintsugi/config.json > kintsugi-config.json
   ```

4. **System info:**
   ```bash
   uname -a
   # macOS
   sw_vers
   # Linux
   lsb_release -a
   ```

### Report Issue

1. Open issue on GitHub
2. Include:
   - Problem description
   - Steps to reproduce
   - Expected vs actual behavior
   - Version information
   - Relevant logs (sanitize sensitive data)
   - Screenshots if applicable

### Community Support

- GitHub Discussions
- Discord server (if available)
- Stack Overflow (tag: kintsugi)

## Preventive Maintenance

Avoid issues with regular maintenance:

### Weekly

- Archive old completed tasks
- Clear terminal history
- Check disk space

### Monthly

- Vacuum database:
  ```bash
  sqlite3 ~/.kintsugi/local.db "VACUUM"
  ```
- Review and clean snapshots:
  ```bash
  du -sh ~/.kintsugi/task-snapshots/
  ```
- Update Kintsugi to latest version

### Quarterly

- Export configuration backup
- Review and clean logs
- Archive old sessions

## Next Steps

- **[Configuration Guide](configuration.md)** - Optimize settings
- **[Architecture](architecture.md)** - Understand system design
- **[Features](features.md)** - Learn feature details
- **[Getting Started](getting-started.md)** - Review setup steps
