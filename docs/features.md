# Features Overview

Kintsugi provides a comprehensive suite of features designed to enhance your Claude-assisted development workflow. This guide covers all major features and how to use them effectively.

## Automatic Task Tracking

Tasks are created automatically when you submit prompts to Claude Code, eliminating manual task management.

### How It Works

1. **Submit a Prompt** to Claude Code in your terminal
2. **Task Created Instantly** - appears on the Kanban board
3. **Status Updates Automatically** as Claude works
4. **Complete Audit Trail** of all activities captured

### Task Information Captured

Each task automatically records:

- **Prompt Text** - Your original request to Claude
- **Git Context** - Repository, branch, and commit SHA
- **Timestamps** - Creation, last update, completion times
- **File Changes** - Before/after snapshots and diffs
- **Token Usage** - Consumption and cost per task
- **Status History** - All state transitions
- **Session ID** - Links to Claude CLI session

### Task Status Workflow

Tasks flow through five states:

| Status               | Description                  | Trigger                     |
| -------------------- | ---------------------------- | --------------------------- |
| **To Do**            | Task created but not started | Prompt submitted            |
| **In Progress**      | Claude is actively working   | First tool use (Edit/Write) |
| **Awaiting Input**   | Waiting for your response    | Claude stops/pauses         |
| **Ready for Review** | Changes complete             | Session ends                |
| **Done**             | Reviewed and accepted        | Manual drag to Done         |

### Manual Task Operations

While tasks are created automatically, you can:

- **Drag and Drop** between columns to change status
- **Edit Details** by clicking the task card
- **Delete Tasks** that are no longer needed
- **Add Comments** for notes and collaboration
- **Link to Epics** for organization
- **Assign JIRA Tickets** for external tracking

## Kanban Board

Visual task organization with drag-and-drop functionality.

### Board Layout

- **5 Columns** - To Do, In Progress, Awaiting Input, Ready for Review, Done
- **Task Cards** - Color-coded by status with key information
- **Epic Cards** - Group related tasks visually
- **Filters** - Search and filter by branch, repository, epic
- **Sort Options** - By date, priority, or custom order

### Task Card Details

Each card displays:

- Prompt text (truncated with "show more")
- Branch and repository info
- Time indicators (created, last updated)
- Token usage badge
- Status indicators
- Quick action buttons

### Board Interactions

- **Drag & Drop** - Move tasks between columns
- **Click Card** - Open detailed task view
- **Hover Actions** - Quick access to common operations
- **Multi-Select** (planned) - Batch operations on tasks

## Integrated Terminal

Full-featured terminal emulation built directly into Kintsugi.

### Features

- **Real Shell** - Runs your system shell (bash, zsh, PowerShell, etc.)
- **Full Terminal Support** - ANSI colors, cursor control, etc.
- **Multiple Tabs** - Open multiple terminal sessions
- **Command History** - Search and replay commands
- **Copy/Paste** - Standard clipboard operations
- **Keyboard Shortcuts** - All standard terminal shortcuts work

### Terminal Integration

- **Linked to Tasks** - Commands associated with active tasks
- **Working Directory** - Automatically detects project context
- **Process Management** - View running processes
- **Output Capture** - Terminal output logged

### Using the Terminal

1. **Open Terminal** - Click terminal button or press Ctrl/Cmd + `
2. **Navigate to Project** - Use standard `cd` commands
3. **Run Claude** - Execute `claude` command as normal
4. **Watch Tasks Appear** - Automatic task creation as you work

### Terminal Settings

Customize in Settings → Terminal:

- **Shell** - Choose default shell (bash, zsh, fish, etc.)
- **Font** - Customize font family and size
- **Theme** - Light or dark terminal theme
- **Scrollback** - Configure history buffer size
- **Cursor Style** - Block, line, or underline

## File Diff Viewer

Review all changes Claude makes before committing to your repository.

### Features

- **Side-by-Side Comparison** - Before and after view
- **Unified Diff Format** - Standard git diff format
- **Syntax Highlighting** - Language-aware coloring
- **Line Numbers** - Easy reference for both versions
- **Context Lines** - See surrounding code
- **File Navigation** - Jump between changed files
- **SonarQube for IDE Integration** - Issues shown inline

### Viewing Diffs

1. **Click "View Diff"** on a task card
2. **Diff Panel Opens** in bottom panel
3. **Select File** from changed files list
4. **Review Changes** line by line
5. **Check Issues** if SonarQube for IDE is enabled

### Diff Actions

- **Copy Changes** - Copy diff to clipboard
- **Open in Editor** - View full file content
- **Accept Changes** - Mark as reviewed
- **Discard Changes** (with git) - Revert changes

### Diff Generation

Diffs are generated automatically:

- **Before Snapshot** - Captured when Claude starts editing
- **After Snapshot** - Current file state when Claude stops
- **Diff Algorithm** - Uses git diff for accuracy
- **Storage** - Snapshots saved to `~/.kintsugi/task-snapshots/`

## Code Quality Analysis

Real-time code analysis powered by SonarQube for IDE and SonarQube.

### SonarQube for IDE Features

- **Real-Time Analysis** - Analyze files as Claude edits them
- **Language Support** - 25+ programming languages
- **Issue Detection** - Bugs, vulnerabilities, code smells
- **Severity Levels** - Blocker, Critical, Major, Minor, Info
- **Rule Descriptions** - Detailed explanations of issues
- **Quick Fixes** - Suggested remediation

### Analysis Workflow

1. **Automatic Trigger** - When Claude edits files
2. **Language Detection** - Based on file extension
3. **Rule Application** - Quality profile rules applied
4. **Issue Display** - Results shown in diff viewer
5. **Review Issues** - Click for details and remediation

### SonarQube Connected Mode

Connect to your team's SonarQube server for:

- **Quality Profiles** - Use team-defined rules
- **Quality Gates** - Check against thresholds
- **Issue Sync** - Maintain consistency with server
- **Project Binding** - Link to SonarQube projects

### Issue Details

Each issue shows:

- **Type** - Bug, Vulnerability, Code Smell, Security Hotspot
- **Severity** - Blocker, Critical, Major, Minor, Info
- **Rule Key** - SonarQube for IDE rule identifier
- **Message** - Description of the issue
- **Location** - File, line, and column
- **Effort** - Estimated time to fix
- **Remediation** - How to resolve the issue

### Supported Languages

Java, JavaScript, TypeScript, Python, C#, C, C++, PHP, Kotlin, Ruby, Scala, Go, Swift, HTML, CSS, XML, YAML, JSON, and more.

## Token Usage Tracking

Monitor Claude API costs automatically with detailed breakdowns.

### What's Tracked

- **Input Tokens** - Prompt and context sent to Claude
- **Output Tokens** - Claude's response
- **Cache Creation** - New cache entries
- **Cache Read** - Cache hits
- **Model Used** - Which Claude model processed the request
- **Cost in USD** - Calculated using current pricing

### Consumption Reports

View token usage at three levels:

1. **Per Task** - See cost for individual tasks
2. **Per Epic** - Aggregate cost for grouped tasks
3. **All Work** - Total consumption across all tasks

### Data Source

Token data is extracted from:

- Claude CLI transcript logs (`~/.claude/logs/`)
- Parsed automatically every 30 seconds
- Calculations use latest Anthropic API pricing
- Historical data retained for analysis

### Usage Display

Token information shown:

- **Task Cards** - Badge with total cost
- **Task Details** - Full breakdown modal
- **Reports** - Consumption report view
- **Summaries** - Daily, weekly, monthly aggregates

### Cost Optimization

Use consumption data to:

- Identify expensive prompts
- Optimize prompt engineering
- Track budget utilization
- Compare task efficiency

## Epic-Based Organization

Group related tasks into epics for large features or initiatives.

### What are Epics?

Epics are containers for related tasks:

- **High-Level Goals** - "Add user authentication", "Refactor API"
- **Multiple Tasks** - Can contain unlimited child tasks
- **Status Aggregation** - Epic status derived from tasks
- **Token Rollup** - Total cost across all tasks
- **JIRA Integration** - Link to JIRA epics

### Creating Epics

1. **Click "New Epic"** on the board
2. **Enter Title** - Brief description of the goal
3. **Add Description** - Detailed objectives
4. **Link JIRA Ticket** (optional)
5. **Add Tasks** - Drag tasks onto epic or select during creation

### Epic Status

Epic status is automatically derived:

- **Planning** - All tasks in "To Do"
- **In Progress** - Some tasks in progress
- **Review** - All tasks complete, awaiting review
- **Done** - All tasks in "Done" column

### AI-Powered Summaries

Generate epic summaries automatically:

- **Click "Generate Summary"** on epic card
- **AI Analyzes** all task prompts and changes
- **Summary Generated** with key accomplishments
- **Manual Edit** - Refine as needed

### Epic Display

Epics appear on the board as:

- **Expanded Cards** - Show all child tasks
- **Collapsed Cards** - Summary only
- **Progress Indicator** - Visual progress bar
- **Token Total** - Cost across all tasks

## Tool Approval Workflow

Control dangerous operations before Claude executes them.

### What Gets Approval Requests?

By default, approval requested for:

- **File Writes** - Creating or overwriting files
- **Bash Commands** - Executing shell commands
- **File Deletions** - Removing files
- **Git Operations** - Commits, pushes, branch operations

### Approval Process

1. **Claude Requests Permission** - Hook intercepts tool call
2. **Request Sent to Backend** - Stored with 120s timeout
3. **Modal Appears** in Kintsugi - Shows operation details
4. **You Decide** - Approve, deny, or modify
5. **Response Sent** - Claude proceeds or stops
6. **Result Logged** - Decision recorded for audit

### Approval Modal

Shows detailed information:

- **Tool Name** - Edit, Write, Bash, etc.
- **Parameters** - Arguments passed to tool
- **Context** - Current task and session
- **Risk Level** - Automatic risk assessment
- **Preview** - For file operations, show content

### Actions Available

- **Approve** - Allow the operation
- **Deny** - Block the operation
- **Modify** - Edit parameters before approval
- **Always Allow** - Skip future approvals for this operation
- **Timeout** - Auto-deny after 120 seconds

### Configuration

Customize approval behavior in Settings:

- **Enable/Disable** - Toggle approval system
- **Tool Selection** - Choose which tools require approval
- **Timeout Duration** - Adjust wait time
- **Auto-Allow Patterns** - Define safe patterns

## JIRA Integration

Link tasks to JIRA for external project management.

### Setup

1. **Open Settings** → JIRA
2. **Enter Credentials**
   - JIRA URL (e.g., `https://yourcompany.atlassian.net`)
   - Email address
   - API token
3. **Test Connection** - Verify credentials
4. **Save Configuration**

### Linking Tasks

- **During Creation** - Enter JIRA ticket key
- **After Creation** - Click "Link JIRA" on task card
- **Automatic Detection** - If prompt mentions ticket (e.g., "PROJ-123")

### JIRA Ticket Display

Linked tickets show:

- **Ticket Key** - Clickable link to JIRA
- **Status** - Current JIRA status
- **Assignee** - Who's responsible
- **Priority** - Ticket priority level
- **Description** - Ticket summary

### Sync Behavior

- **One-Way Sync** - Kintsugi reads from JIRA
- **No Auto-Update** - JIRA tickets not modified by Kintsugi
- **Manual Refresh** - Click refresh to update ticket data
- **Cache Duration** - 5 minutes

## Real-Time Synchronization

Multiple Kintsugi instances stay synchronized via WebSockets.

### WebSocket Events

Real-time updates for:

- **Task Created** - New tasks appear instantly
- **Task Updated** - Status changes propagate
- **Epic Modified** - Epic changes reflected
- **Approval Created** - Approval requests broadcast
- **Analysis Complete** - SonarQube for IDE results shared
- **Comment Added** - Comments appear immediately

### Connection Management

- **Automatic Connection** - Connects on app launch
- **Reconnection** - Automatic with exponential backoff
- **Connection Status** - Indicator in UI
- **Offline Mode** - App continues working locally

### Multi-Instance Support

Run Kintsugi on multiple machines:

- **Shared Database** - If using shared `~/.kintsugi/` directory
- **Independent Databases** - Each machine has own data
- **Sync via Git** - Commit database to share (not recommended)

## Session Management

Track Claude CLI sessions and associate with tasks.

### Session Information

Each session captures:

- **Session ID** - Claude CLI session identifier
- **Start/End Time** - Duration of session
- **Repository** - Git repository context
- **Branch** - Working branch
- **Tasks Created** - All tasks from session
- **Token Usage** - Total consumption

### Session Lifecycle

1. **Session Start** - `SessionStart` hook fires
2. **Tasks Created** - Associated with session
3. **Session Active** - Monitored by watcher
4. **Session End** - `SessionEnd` hook fires
5. **Tasks Moved** - To "Ready for Review" status

### Session Views

- **Active Sessions** - Currently running
- **Session History** - Past sessions
- **Session Details** - Full activity log
- **Consumption Report** - Cost per session

## Comments and Collaboration

Add notes and collaborate on tasks with comments.

### Adding Comments

1. **Open Task Details** - Click task card
2. **Click "Add Comment"** - In comments section
3. **Enter Text** - Markdown supported
4. **Submit** - Comment saved with timestamp

### Comment Features

- **Markdown Support** - Format with markdown syntax
- **Timestamps** - When comment was added
- **User Attribution** - Who added the comment
- **Edit/Delete** - Modify or remove comments
- **Threading** (planned) - Reply to comments

### Use Cases

- **Review Notes** - Document review findings
- **Questions** - Ask about implementation choices
- **Decisions** - Record architectural decisions
- **Reminders** - Note follow-up work

## Keyboard Shortcuts

Efficient navigation with keyboard shortcuts.

### Global Shortcuts

- `Ctrl/Cmd + ,` - Open Settings
- `Ctrl/Cmd + K` - Focus search
- `Ctrl/Cmd + N` - New task (manual)
- `Ctrl/Cmd + E` - New epic
- `Ctrl/Cmd + Q` - Quit application

### Terminal Shortcuts

- `Ctrl/Cmd + ` ` - Toggle terminal panel
- `Ctrl/Cmd + T` - New terminal tab
- `Ctrl/Cmd + W` - Close current tab
- `Ctrl/Cmd + [1-9]` - Switch to tab N

### Task Shortcuts

- `Enter` - Open selected task
- `Delete` - Delete selected task
- `Arrow Keys` - Navigate between tasks
- `Esc` - Close task details

### Customization

Customize shortcuts in Settings → Keyboard:

- **View All Shortcuts** - Complete list
- **Rebind Keys** - Change default bindings
- **Reset to Defaults** - Restore original shortcuts

## Search and Filters

Find tasks quickly with powerful search and filtering.

### Search Features

- **Text Search** - Search by prompt text, comments, etc.
- **Fuzzy Matching** - Finds partial matches
- **Real-Time Results** - Updates as you type
- **Highlight Matches** - Matched text highlighted

### Filter Options

- **Status** - Show only specific status
- **Branch** - Filter by git branch
- **Repository** - Filter by repository
- **Epic** - Show tasks in specific epic
- **Date Range** - Tasks created in time period
- **Has Issues** - Tasks with SonarQube for IDE issues

### Saved Filters

- **Create Filter** - Save common filter combinations
- **Quick Access** - One-click filter application
- **Share Filters** (planned) - Share with team

## Next Steps

Now that you understand all features:

1. **[Learn Common Workflows](workflows.md)** - See features in action
2. **[Configure Integrations](integrations.md)** - Set up SonarQube, JIRA
3. **[Customize Settings](configuration.md)** - Tailor to your workflow
4. **[Troubleshooting](troubleshooting.md)** - Fix common issues
