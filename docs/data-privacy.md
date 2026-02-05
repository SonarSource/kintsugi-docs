# Data & Privacy

This document explains how Kintsugi handles your data, what information is collected, where it's stored, and how your privacy is protected.

## Core Privacy Principles

Kintsugi is built with privacy as a foundational principle:

1. **Local-First Architecture** - All sensitive data stays on your machine
2. **No Cloud Storage Required** - Works completely offline
3. **User-Controlled Data** - You own and control all your data
4. **Minimal Telemetry** - Only anonymous usage data (opt-out available)
5. **Open Architecture** - Transparent data handling

## What Data Is Stored

### On Your Local Machine

All application data is stored in `~/.kintsugi/` directory:

#### Database (`local.db`)

SQLite database containing:

**Task Data:**

- Prompt text
- Git context (repository path, branch name, commit SHA)
- File paths changed
- Diffs generated
- Status and timestamps
- Token usage and cost
- Session associations

**Epic Data:**

- Epic titles and descriptions
- Task associations
- AI-generated summaries
- Status derived from tasks

**Session Data:**

- Session IDs from Claude CLI
- Start/end timestamps
- Repository and branch context
- Total token consumption

**Analysis Data:**

- SonarQube for IDE analysis results
- Issue details (type, severity, message, location)
- Rule information
- Timestamps of analyses

**Integration Data:**

- SonarQube credentials (encrypted)
- JIRA credentials (encrypted)
- GitHub tokens (encrypted)
- Project bindings and mappings

**Approval Data:**

- Tool approval requests
- Your approval/denial decisions
- Timestamps and parameters

**Repository Metadata:**

- Local repository paths
- Git remote URLs
- Branch information
- Last accessed timestamps

**Telemetry Events** (if enabled):

- Anonymous usage events
- Device ID (random UUID)
- Timestamps
- Event types and basic metrics

#### Configuration Files

**`config.json`**

- Backend URL (default: `http://localhost:63421`)
- Authentication token (auto-generated)
- Log level

**`desktop-config.json`**

- UI preferences (theme, font size, etc.)
- Window size and position
- Integration credentials
- Last opened repository
- User settings

#### File Snapshots

**`task-snapshots/tasks/{taskId}/`**

For each task, before/after snapshots of edited files:

- Original file content (`.before`)
- Modified file content (`.after`)
- Full file contents, not just diffs
- Preserves all code including sensitive data

**Important:** Snapshots may contain:

- Sensitive code
- API keys or credentials in files
- Proprietary algorithms
- Personal comments

#### Log Files

**`local-backend.log`**

- Backend server activity
- API requests and responses
- Database operations
- Error messages

**`hook-debug.log`**

- Hook execution logs
- Git operations
- Snapshot creation
- API communication

**Logs may contain:**

- File paths
- Git branch names
- Error stack traces
- Request payloads

### Claude CLI Data

Kintsugi reads (but doesn't store) data from Claude CLI:

**Transcript Logs** (`~/.claude/logs/`)

- API request/response pairs
- Token usage statistics
- Model information
- Session metadata

**Settings** (`~/.claude/settings.json`)

- Hook registrations
- Claude CLI configuration

Kintsugi **reads** these files but **never modifies** them (except for hook registration).

### What Is NOT Stored Locally

- Claude API key (stored only by Claude CLI)
- Full conversation history (only prompts you submit)
- Claude's responses (unless in diffs)
- API request details beyond token usage

## Data Transmitted Over Network

### To Local Backend (localhost:63421)

All communication between desktop app and backend is on localhost:

**Task Operations:**

- Task CRUD operations
- Epic management
- Session tracking

**Analysis Requests:**

- File content for SonarQube for IDE analysis
- Analysis results

**Hook Events:**

- Prompt text
- Git context
- File snapshots
- Tool parameters

**No External Network:** All communication is local, never leaves your machine.

### To External Services (Optional Integrations)

#### SonarQube (Optional)

**What's Sent:**

- Authentication token
- Project key
- File content for analysis
- Language information

**What's Received:**

- Quality profiles
- Rule definitions
- Analysis results

**Control:** You control when analysis occurs and which files are sent.

#### JIRA (Optional)

**What's Sent:**

- Authentication credentials
- Ticket key for lookup

**What's Received:**

- Ticket metadata (status, assignee, description, etc.)
- Comments (if enabled)

**Control:** Only requested tickets are fetched. Kintsugi never writes to JIRA.

#### GitHub (Optional)

**What's Sent:**

- Personal access token
- Repository owner/name
- API requests for repository data

**What's Received:**

- Repository metadata
- Branch information

**Control:** Only when you explicitly use GitHub features.

#### Anthropic API (via Claude CLI)

Kintsugi **does not** communicate with Anthropic directly. All Claude interactions go through Claude CLI, which handles API communication.

**Indirect Access:**

- Kintsugi reads transcript logs to calculate token usage
- Never sends data to Anthropic servers
- Never accesses your API key

### Telemetry (Optional, Opt-Out Available)

If telemetry is enabled, anonymous usage data is sent to telemetry backend:

**What's Sent:**

- Device ID (random UUID, not linked to identity)
- Event type (e.g., "task_created", "approval_granted")
- Timestamp
- Version information
- Basic metrics (task count, session duration)

**What's NOT Sent:**

- Prompt text
- Code content
- File paths
- Repository names
- Personal information
- IP address (not logged)

**Purpose:**

- Understand feature usage
- Identify bugs and crashes
- Improve user experience
- Prioritize development

**Control:**

- Disable in Settings → Privacy → Telemetry
- Or set environment variable: `KINTSUGI_DISABLE_TELEMETRY=true`

## Data Security

### Storage Security

**File System Permissions:**

- `~/.kintsugi/` directory is user-read-write only (700)
- Database file: 600 permissions
- Config files: 600 permissions
- Logs: 644 permissions

**Encryption:**

- Database: Not encrypted at rest (SQLite limitation)
- Credentials: Encrypted using OS keychain (planned)
- Network: All external communication over HTTPS

**Access Control:**

- Only your user account can access Kintsugi data
- Backend binds to localhost only (127.0.0.1)
- No external network exposure

### Credential Storage

**Current Implementation:**

- Credentials stored in `desktop-config.json`
- Base64 encoded (not encrypted)
- File has restricted permissions (600)

**Planned Enhancement:**

- OS keychain integration
- Hardware encryption support
- Vault integration option

**Best Practices:**

- Use tokens with minimal required permissions
- Rotate tokens regularly
- Don't share your `~/.kintsugi/` directory
- Use separate tokens for different tools

### Network Security

**Local Communication:**

- Backend on localhost only
- Authentication via bearer token
- Token auto-generated on first run

**External Communication:**

- HTTPS for all external integrations
- Certificate validation enforced
- Token-based authentication
- No passwords sent in plain text

## Data Retention

### Automatic Retention

**Tasks:**

- Stored indefinitely by default
- No automatic deletion

**Snapshots:**

- Stored indefinitely by default
- Can grow large over time

**Logs:**

- Rotated when exceeding 10MB
- Last 5 log files kept

**Telemetry:**

- Stored on telemetry backend for 90 days
- Then automatically deleted

### Manual Cleanup

**Delete Individual Tasks:**

- Click task → Delete
- Removes task and associated snapshots

**Archive Old Tasks:**

- Bulk archive completed tasks
- Moves to archive table
- Reduces active data size

**Clear Snapshots:**

```bash
rm -rf ~/.kintsugi/task-snapshots/
```

**Vacuum Database:**

```bash
sqlite3 ~/.kintsugi/local.db "VACUUM"
```

**Clear Logs:**

```bash
rm ~/.kintsugi/*.log
```

## Data Portability

### Export Data

**Export Tasks:**

- Settings → Data → Export Tasks
- Exports to JSON or CSV
- Includes all task metadata

**Export Configuration:**

- Settings → Advanced → Export Configuration
- Saves all settings to JSON
- Portable to other machines

**Database Backup:**

```bash
cp ~/.kintsugi/local.db ~/backup/kintsugi-backup-$(date +%Y%m%d).db
```

**Snapshots Backup:**

```bash
tar -czf ~/backup/snapshots-$(date +%Y%m%d).tar.gz ~/.kintsugi/task-snapshots/
```

### Import Data

**Import Tasks:**

- Settings → Data → Import Tasks
- Supports JSON and CSV formats

**Import Configuration:**

- Settings → Advanced → Import Configuration
- Restores settings from exported JSON

**Database Restore:**

```bash
cp ~/backup/kintsugi-backup-20240101.db ~/.kintsugi/local.db
```

## Data Deletion

### Remove Application Data

**Uninstall Kintsugi:**

```bash
# macOS
rm -rf ~/Applications/Kintsugi.app
rm -rf ~/.kintsugi/
rm -rf ~/Library/Logs/Kintsugi/

# Linux
rm -rf ~/.config/Kintsugi/
rm -rf ~/.kintsugi/

# Windows
# Uninstall via Control Panel
# Delete %APPDATA%\Kintsugi
# Delete %USERPROFILE%\.kintsugi
```

**Unregister Hooks:**

```bash
kintsugi unregister-hooks
```

Edit `~/.claude/settings.json` to verify hooks removed.

**Delete Plugin:**

```bash
npm uninstall -g @kintsugi/plugin
```

### Remove Integration Data

**SonarQube:**

- Settings → SonarQube → Disconnect
- Manually revoke token in SonarQube

**JIRA:**

- Settings → JIRA → Disconnect
- Manually revoke token in Atlassian Account

**GitHub:**

- Settings → GitHub → Disconnect
- Manually revoke token in GitHub Settings

## Privacy Compliance

### GDPR Compliance

Kintsugi respects GDPR principles:

**Right to Access:**

- All data is stored locally and accessible to you
- Export features provide structured access

**Right to Erasure:**

- Delete individual tasks or all data
- Uninstall removes all local data

**Right to Portability:**

- Export to standard formats (JSON, CSV)
- Database is SQLite (open format)

**Right to Rectification:**

- Edit task data directly
- Modify any stored information

**Data Minimization:**

- Only essential data is stored
- No unnecessary collection

**Purpose Limitation:**

- Data used only for stated purposes
- No secondary use without consent

### No Personal Information Collection

Kintsugi **does not** collect:

- Name
- Email (except for integration credentials)
- IP address
- Location
- Biometric data
- Payment information

## Sensitive Data Handling

### Code and Secrets

**Risk:** File snapshots may contain:

- API keys
- Passwords
- Secrets in code

**Recommendations:**

1. **Use .env files** - Keep secrets in environment variables
2. **Never commit secrets** - Use `.gitignore` for sensitive files
3. **Review snapshots** - Check what's being captured
4. **Clean up regularly** - Delete old snapshots with secrets
5. **Use secret managers** - Store secrets in vault systems

**Auto-Detection:** (Planned)

- Detect common secret patterns
- Warn before capturing sensitive files
- Option to exclude from snapshots

### Third-Party Access

**SonarQube:**

- If you send files for analysis, SonarQube server receives file content
- Check your organization's SonarQube data policy

**JIRA:**

- Only ticket metadata is fetched
- JIRA sees which tickets you access

**GitHub:**

- Only repository metadata is accessed
- GitHub sees which repos you query

## Telemetry Details

### What Telemetry Collects

**Events Tracked:**

- `app_started` - Application launch
- `task_created` - Task creation
- `task_completed` - Task completion
- `approval_granted` - Approval given
- `approval_denied` - Approval denied
- `sonarlint_analysis` - Code analysis run
- `epic_created` - Epic creation
- `integration_connected` - Integration setup

**Metadata Included:**

- Device ID (random UUID)
- Timestamp
- Event type
- Basic counts (e.g., number of tasks)

**Metadata NOT Included:**

- Prompt text
- Code content
- File paths
- Repository names
- Branch names
- Personal information

### Opt-Out

**Disable Telemetry:**

1. **In Application:**
   - Settings → Privacy → Disable Telemetry

2. **Environment Variable:**

   ```bash
   export KINTSUGI_DISABLE_TELEMETRY=true
   ```

3. **Configuration File:**
   Edit `~/.kintsugi/config.json`:
   ```json
   {
     "telemetry": {
       "enabled": false
     }
   }
   ```

**Verify Opt-Out:**

```bash
grep telemetry ~/.kintsugi/config.json
```

No telemetry should be sent after disabling.

## Third-Party Services

### Error Tracking (Sentry)

**What's Sent:**

- Error messages
- Stack traces
- Device ID
- Version information

**What's NOT Sent:**

- User data
- Code content
- File paths (sanitized)

**Purpose:**

- Identify bugs
- Improve stability
- Prioritize fixes

**Control:**

- Settings → Privacy → Error Reporting
- Disable to stop sending error reports

## Open Source Transparency

Kintsugi's architecture is designed for transparency:

- **SQLite Database** - Human-readable schema, standard tools
- **JSON Configuration** - Plain text, easy to inspect
- **Local Files** - No hidden cloud storage
- **Open Protocols** - Standard HTTP, WebSocket, REST

You can inspect all data at any time:

```bash
# View database schema
sqlite3 ~/.kintsugi/local.db ".schema"

# Query tasks
sqlite3 ~/.kintsugi/local.db "SELECT * FROM tasks LIMIT 5"

# View configuration
cat ~/.kintsugi/config.json

# Check snapshots
ls -la ~/.kintsugi/task-snapshots/
```

## Questions and Concerns

If you have privacy questions or concerns:

1. **Review Documentation** - This page covers most scenarios
2. **Inspect Data** - Use SQLite tools to examine database
3. **Open Issue** - GitHub issues for privacy questions
4. **Contact Team** - Email for sensitive privacy concerns

## Best Practices

To maximize privacy and security:

1. **Use Separate Tokens**
   - Different tokens for each integration
   - Minimal required permissions
   - Rotate regularly

2. **Review Credentials**
   - Audit stored credentials quarterly
   - Remove unused integrations
   - Update expired tokens

3. **Clean Up Data**
   - Archive old tasks monthly
   - Delete unnecessary snapshots
   - Clear old logs

4. **Secure Your Machine**
   - Use disk encryption
   - Enable firewall
   - Keep OS updated
   - Use strong account password

5. **Be Mindful of Secrets**
   - Don't commit secrets to git
   - Use environment variables
   - Review diffs before committing

6. **Limit Telemetry**
   - Disable if privacy-sensitive environment
   - Review telemetry settings

7. **Regular Backups**
   - Backup `~/.kintsugi/` regularly
   - Store backups securely
   - Test restore process

## Summary

- **All data stored locally** on your machine
- **No cloud storage required** - works fully offline
- **Optional integrations** - you control what connects
- **Minimal telemetry** - anonymous, opt-out available
- **Open architecture** - inspect data anytime
- **User-controlled** - you own your data
- **Credentials stored locally** - secure your machine
- **Snapshots contain code** - may include secrets
- **Integration data shared** - with services you connect

Kintsugi is designed to respect your privacy while providing powerful development tools. Your data stays yours.

## Next Steps

- **[Configuration Guide](configuration.md)** - Configure privacy settings
- **[Integrations Guide](integrations.md)** - Understand integration data
- **[Troubleshooting](troubleshooting.md)** - Fix privacy-related issues
- **[Architecture](architecture.md)** - Technical data flow details
