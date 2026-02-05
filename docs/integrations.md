# Integrations Guide

Kintsugi integrates with popular development tools to enhance your workflow. This guide provides detailed setup instructions and best practices for each integration.

## SonarQube Integration

Connect Kintsugi to your SonarQube server for team-wide code quality standards.

### Prerequisites

- SonarQube Server 9.9+ or SonarCloud account
- Personal access token or user credentials
- Network access to SonarQube server

### Setup Steps

#### 1. Generate SonarQube Token

**For SonarQube Server:**

1. Log in to your SonarQube instance
2. Click your avatar → My Account
3. Navigate to Security tab
4. Under "Generate Tokens":
   - Name: `Kintsugi`
   - Type: User Token
   - Expires in: No expiration (or set desired duration)
5. Click "Generate"
6. **Copy the token immediately** (only shown once)

**For SonarCloud:**

1. Log in to SonarCloud
2. Click your avatar → My Account
3. Go to Security tab
4. Generate token with name `Kintsugi`
5. Copy token

#### 2. Configure in Kintsugi

1. **Open Settings** → SonarQube
2. **Enter Details:**
   - **Server URL**:
     - SonarQube: `https://sonarqube.yourcompany.com`
     - SonarCloud: `https://sonarcloud.io`
   - **Authentication Method**: Token (recommended)
   - **Token**: Paste your generated token
   - **Organization** (SonarCloud only): Your org key

3. **Test Connection**
   - Click "Test Connection" button
   - Should show: ✅ Connection successful

4. **Save Configuration**

#### 3. Bind Project

1. **Select Repository** in Kintsugi
2. **Click "Bind to SonarQube Project"**
3. **Choose Organization** (if using SonarCloud)
4. **Select Project** from dropdown
   - Projects list populated from SonarQube
   - Search to find your project

5. **Select Quality Profile**
   - Choose language-specific profiles
   - Or use server defaults

6. **Sync Rules**
   - Click "Sync Now"
   - Downloads rule definitions
   - May take 30-60 seconds

7. **Save Binding**

### Quality Profiles

Quality profiles define which rules to apply.

#### Using Server Quality Profiles

**Advantages:**
- Consistency with CI/CD
- Team-wide standards
- Centrally managed

**Setup:**
1. Configure profiles in SonarQube UI
2. Assign to project
3. Sync in Kintsugi

#### Synchronization

**Automatic Sync:**
- On Kintsugi startup
- Configurable frequency

**Manual Sync:**
- Settings → SonarQube → Sync Now
- Recommended after profile changes

### Analysis Configuration

#### Supported Languages

SonarLint analyzes 25+ languages:
- **Java, JavaScript, TypeScript**
- **Python, C#, C, C++**
- **PHP, Kotlin, Ruby, Scala**
- **Go, Swift, Objective-C**
- **HTML, CSS, XML, YAML, JSON**
- And more

#### Language Detection

Automatic based on file extension:
- `.js` → JavaScript
- `.ts` → TypeScript
- `.py` → Python
- `.java` → Java
- etc.

#### Analysis Triggers

**Automatic:**
- When Claude edits files
- On manual "Analyze" click

**Configuration:**
- Enable/disable in Settings → Code Analysis
- Set file size limits
- Configure timeouts

### Viewing Results

#### In Diff Viewer

1. Click "View Diff" on task
2. Issues shown inline
3. Hover over issue marker
4. Click for full details

#### Issue Details

Each issue shows:
- **Type**: Bug, Vulnerability, Code Smell, Security Hotspot
- **Severity**: Blocker, Critical, Major, Minor, Info
- **Rule**: SonarLint rule key (e.g., `java:S1234`)
- **Message**: Description of the issue
- **Location**: Line and column numbers
- **Effort**: Estimated time to fix
- **Description**: Full rule documentation
- **Code Example**: How to fix

#### Filtering Issues

- By severity
- By type
- By file
- By rule

### Troubleshooting SonarQube Integration

#### Connection Issues

**Cannot connect to server**
- Verify server URL is correct
- Check network connectivity: `curl https://sonarqube.yourcompany.com`
- Verify firewall allows connection
- Test with browser first

**Authentication fails**
- Verify token is valid
- Check token hasn't expired
- Ensure token has required permissions
- Try regenerating token

#### Project Binding Issues

**No projects in dropdown**
- Verify you have access to projects in SonarQube
- Check organization is correct (SonarCloud)
- Refresh projects list
- Verify token permissions

**Sync fails**
- Check internet connection
- Verify quality profile exists
- Check SonarQube server is responsive
- Review error logs

#### Analysis Issues

**Files not analyzed**
- Check language is supported
- Verify file size within limits (default 10MB)
- Check SonarLint Bridge is running
- Review bridge logs

**Wrong rules applied**
- Re-sync quality profile
- Verify correct profile selected
- Check binding is saved
- Clear cache and restart

## JIRA Integration

Link Kintsugi tasks to JIRA tickets for external project management.

### Prerequisites

- JIRA Cloud or JIRA Server 8.0+
- JIRA account with project access
- API token (JIRA Cloud) or password (JIRA Server)

### Setup Steps

#### 1. Create JIRA API Token

**For JIRA Cloud:**

1. Go to [Atlassian Account Settings](https://id.atlassian.com/)
2. Click Security → API tokens
3. Click "Create API token"
4. Label: `Kintsugi`
5. Click Create
6. Copy token

**For JIRA Server:**

Use your JIRA password (API tokens not supported)

#### 2. Configure in Kintsugi

1. **Open Settings** → JIRA
2. **Enter Details:**
   - **JIRA URL**:
     - Cloud: `https://yourcompany.atlassian.net`
     - Server: `https://jira.yourcompany.com`
   - **Email**: Your JIRA account email
   - **API Token/Password**: Paste token or enter password
   - **Default Project** (optional): Auto-link tickets from this project

3. **Test Connection**
   - Click "Test Connection"
   - Verifies credentials and access

4. **Save Configuration**

### Linking Tasks to JIRA

#### During Task Creation

Include ticket key in Claude prompt:

```bash
claude "PROJ-123: Implement user profile page"
```

Kintsugi auto-detects and links the ticket.

#### After Task Creation

1. Click task card
2. Click "Link JIRA Ticket"
3. Enter ticket key (e.g., `PROJ-123`)
4. Click "Link"

#### Automatic Detection

Keywords recognized in prompts:
- `PROJ-123`
- `Fix PROJ-123`
- `Resolve PROJ-123`
- `Close PROJ-123`

### Viewing JIRA Tickets

#### In Task Card

- JIRA badge with ticket key
- Clickable link to JIRA
- Status indicator

#### In Task Details

Full ticket information:
- **Summary**: Ticket title
- **Status**: Current JIRA status
- **Assignee**: Who's responsible
- **Priority**: Ticket priority
- **Description**: Full ticket description
- **Created**: When ticket was created
- **Updated**: Last update time

#### Refresh Data

- Manual: Click refresh icon
- Automatic: Every 5 minutes
- On task open: Always fetches latest

### Project Mapping

Map repositories to JIRA projects:

1. **Settings → JIRA → Project Mapping**
2. **Add Mapping:**
   - Repository: `/path/to/repo`
   - JIRA Project: `PROJ`
3. **Save**

Now tickets from that project auto-link when working in that repo.

### Sync Behavior

**One-Way Sync:**
- Kintsugi reads from JIRA
- JIRA is never modified by Kintsugi
- Manual updates in JIRA required

**What Syncs:**
- Ticket metadata
- Status changes
- Assignee changes
- Priority changes
- Comments (if enabled)

**What Doesn't Sync:**
- Task status → JIRA status
- Task completion → JIRA resolution
- Task comments → JIRA comments

### Troubleshooting JIRA Integration

#### Connection Issues

**Cannot connect**
- Verify JIRA URL is correct
- Check network connectivity
- Test URL in browser
- Verify no VPN required

**Authentication fails**
- Check email is correct
- Verify API token is valid
- Try regenerating token
- Check Atlassian account status

#### Ticket Issues

**Ticket not found**
- Verify ticket key is correct
- Check you have permission to view
- Ensure ticket isn't deleted
- Try accessing in JIRA first

**Data not updating**
- Click refresh icon
- Check cache timeout setting
- Clear cache in settings
- Verify JIRA is accessible

## GitHub Integration

Integrate GitHub for pull request management and repository operations.

### Prerequisites

- GitHub account
- Repository access (read/write)
- Personal access token

### Setup Steps

#### 1. Generate GitHub Token

1. Go to [GitHub Settings](https://github.com/settings/tokens)
2. Click "Developer settings" → "Personal access tokens" → "Tokens (classic)"
3. Click "Generate new token" → "Generate new token (classic)"
4. Token description: `Kintsugi`
5. Select scopes:
   - `repo` (Full control of private repositories)
   - `read:org` (Read org and team membership)
6. Click "Generate token"
7. **Copy token** (only shown once)

#### 2. Configure in Kintsugi

1. **Open Settings** → GitHub
2. **Enter Token**
   - Paste your personal access token
3. **Test Connection**
   - Verifies token and permissions
4. **Save Configuration**

### Repository Detection

Kintsugi auto-detects GitHub repositories:

**From Git Remote:**
```bash
git remote -v
# origin  https://github.com/user/repo.git (fetch)
```

**Supported URL Formats:**
- `https://github.com/user/repo.git`
- `git@github.com:user/repo.git`
- `https://github.com/user/repo`

### Features

#### Repository Operations

- Clone repositories
- View repository info
- List branches
- Repository metadata

#### Pull Request Integration

**Note:** While PR integration code exists, it's not part of the final application per your requirements. This section would be excluded in a production-ready doc.

### Troubleshooting GitHub Integration

#### Token Issues

**Token invalid**
- Verify token is correct
- Check token hasn't expired
- Ensure required scopes selected
- Regenerate if needed

**Permission denied**
- Check repository access
- Verify org membership
- Ensure token has `repo` scope

#### Repository Issues

**Repository not detected**
- Verify git remote is configured
- Check URL format is supported
- Ensure .git directory exists
- Try re-initializing git

## Claude Code Integration

Kintsugi integrates deeply with Claude Code CLI via hooks.

### Hook Registration

Hooks are registered in `~/.claude/settings.json`.

#### Automatic Registration

During onboarding, Kintsugi automatically registers hooks.

#### Manual Registration

If automatic registration fails:

```bash
kintsugi register-hooks
```

Or with custom path:
```bash
kintsugi register-hooks --claude-settings ~/.claude/settings.json
```

#### Verify Registration

Check `~/.claude/settings.json`:

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

### Hook Behavior

#### UserPromptSubmit

**Trigger:** When you submit a prompt to Claude

**Actions:**
- Captures prompt text
- Gets git context (repo, branch)
- Creates task in Kintsugi
- Returns task ID

#### PreToolUse

**Trigger:** Before Claude executes a tool (Edit, Write, Bash, etc.)

**Actions:**
- Captures original file content
- Saves snapshot to `~/.kintsugi/task-snapshots/`
- Logs tool parameters

#### PostToolUse

**Trigger:** After Claude executes a tool

**Actions:**
- Records tool completion
- Updates task status to "in_progress"
- Triggers SonarLint analysis (if enabled)

#### Stop

**Trigger:** When Claude stops and waits for input

**Actions:**
- Generates diff from snapshots
- Stores diff in task
- Updates status to "awaiting_input"

#### SessionStart

**Trigger:** When Claude CLI session starts

**Actions:**
- Creates session record
- Associates with repository

#### SessionEnd

**Trigger:** When Claude CLI session ends

**Actions:**
- Updates session end time
- Moves tasks to "ready_for_review"
- Calculates total consumption

#### Notification

**Trigger:** When Claude sends status messages

**Actions:**
- Logs notification
- Updates UI with status

#### PermissionRequest

**Trigger:** When Claude needs permission for an operation

**Actions:**
- Creates approval request
- Shows modal in Kintsugi
- Polls for user decision
- Returns approval/denial to Claude

### Troubleshooting Hook Integration

#### Hooks Not Firing

**Check registration:**
```bash
cat ~/.claude/settings.json | grep hooks
```

**Re-register:**
```bash
kintsugi unregister-hooks
kintsugi register-hooks
```

**Check permissions:**
```bash
ls -la ~/.kintsugi/
chmod +x /path/to/kintsugi/hooks/*.js
```

#### Tasks Not Created

**Check backend:**
```bash
curl http://localhost:63421/health
```

**Check logs:**
```bash
tail -f ~/.kintsugi/hook-debug.log
tail -f ~/.kintsugi/local-backend.log
```

**Verify plugin:**
```bash
kintsugi --version
```

#### Diffs Not Generated

**Check snapshots:**
```bash
ls -la ~/.kintsugi/task-snapshots/tasks/
```

**Test diff generation:**
```bash
kintsugi generate-diff --task-id <task-id>
```

**Check Stop hook:**
```bash
grep "on-stop" ~/.claude/settings.json
```

## Best Practices

### Multi-Integration Setup

When using multiple integrations:

1. **SonarQube** - Set up first for code quality
2. **JIRA** - Link project management
3. **GitHub** - Enable repository operations

### Team Consistency

For team environments:

1. **Export Configuration**
   - Settings → Advanced → Export
   - Share with team

2. **Standard Setup**
   - Same SonarQube server
   - Same JIRA instance
   - Same quality profiles

3. **Documentation**
   - Document team setup
   - Include credential creation steps
   - Note any custom configurations

### Security

**Token Management:**
- Use tokens with minimal scopes
- Rotate tokens regularly
- Don't share tokens
- Use separate tokens per tool

**Credential Storage:**
- Stored locally in `~/.kintsugi/desktop-config.json`
- Never committed to git
- Encrypted at rest (OS keychain integration planned)

**Network Security:**
- Use HTTPS for all connections
- Verify SSL certificates
- Use VPN if required

## Next Steps

- **[Configuration Guide](configuration.md)** - Customize integrations
- **[Workflows](workflows.md)** - Use integrations in practice
- **[Troubleshooting](troubleshooting.md)** - Fix integration issues
- **[Features](features.md)** - Learn integration features
