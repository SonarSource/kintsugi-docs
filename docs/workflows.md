# User Workflows

This guide demonstrates common workflows and use cases for Kintsugi. Each workflow shows step-by-step how to accomplish typical development tasks.

## Basic Development Workflow

The fundamental workflow for using Kintsugi with Claude Code.

### Steps

1. **Open Kintsugi**
   - Launch the application
   - Backend and SonarQube for IDE Bridge start automatically

2. **Open Terminal**
   - Click terminal button or press `Ctrl/Cmd + ` `
   - Terminal opens at your last working directory

3. **Navigate to Project**

   ```bash
   cd ~/projects/my-app
   ```

4. **Submit Prompt to Claude**

   ```bash
   claude "Add input validation to the login form"
   ```

5. **Task Created Automatically**
   - New task appears in "To Do" column
   - Task card shows your prompt and git context

6. **Monitor Progress**
   - Task moves to "In Progress" when Claude starts editing
   - Watch as files are modified
   - Approval modals appear for dangerous operations

7. **Review Changes**
   - Task moves to "Awaiting Input" when Claude pauses
   - Click "View Diff" to see changes
   - Review SonarQube for IDE issues if any

8. **Provide Feedback**
   - Continue conversation with Claude if needed
   - Request modifications or ask questions

9. **Complete Task**
   - When satisfied, drag task to "Done"
   - Commit changes in terminal:
   ```bash
   git add .
   git commit -m "Add input validation to login form"
   git push
   ```

## Epic-Based Feature Development

Use epics to organize development of large features.

### Scenario: Building User Authentication

1. **Create Epic**
   - Click "New Epic" button
   - Title: "User Authentication System"
   - Description: "Implement complete user authentication with login, signup, and password reset"

2. **Break Down into Tasks**
   Submit separate prompts for each component:

   ```bash
   claude "Create user database schema with migrations"
   claude "Implement login API endpoint with JWT"
   claude "Add signup form component with validation"
   claude "Create password reset email flow"
   ```

3. **Link Tasks to Epic**
   - As each task is created, link it to the epic
   - Drag task cards onto epic card, or
   - Edit task and select epic from dropdown

4. **Track Progress**
   - Epic card shows aggregate status
   - Progress bar indicates completion
   - Total token cost displayed

5. **Generate Summary**
   - Once all tasks complete, click "Generate Summary" on epic
   - AI creates summary of all changes
   - Edit summary and add notes

6. **Review as a Unit**
   - Review all task diffs together
   - Check for consistency across tasks
   - Verify all requirements met

7. **Complete Epic**
   - Move all tasks to "Done"
   - Epic status automatically updates
   - Close epic or archive

## Code Quality Review Workflow

Use SonarQube for IDE to ensure code quality as Claude makes changes.

### Setup

1. **Enable SonarQube for IDE**
   - Settings → SonarQube for IDE
   - Ensure SonarQube for IDE Bridge is running
   - Configure quality profiles if using SonarQube

2. **Set Analysis Preferences**
   - Enable automatic analysis on file changes
   - Choose languages to analyze
   - Set severity threshold for notifications

### During Development

1. **Claude Edits Files**
   - SonarQube for IDE automatically analyzes changes
   - Issues detected in real-time

2. **View Issues in Diff**
   - Click "View Diff" on task
   - Issues shown inline with code
   - Click issue for details

3. **Review Issue Details**
   - **Type**: Bug, Vulnerability, Code Smell
   - **Severity**: Blocker, Critical, Major, Minor, Info
   - **Rule**: SonarQube for IDE rule identifier
   - **Message**: What's wrong
   - **Remediation**: How to fix

4. **Request Fixes**
   - If issues found, ask Claude to fix:

   ```bash
   > Fix the SonarQube for IDE issues in the UserController
   ```

5. **Re-Analyze**
   - SonarQube for IDE automatically re-analyzes
   - Verify issues are resolved

6. **Accept Changes**
   - Once issues resolved, mark task as done
   - Commit clean code

### Best Practices

- **Review Issues Promptly** - Address issues while context is fresh
- **Prioritize by Severity** - Fix blockers and critical issues first
- **Learn from Issues** - Use as learning opportunities
- **Configure Rules** - Disable noisy rules, enable important ones

## Tool Approval Workflow

Control dangerous operations with the approval system.

### Scenario: Claude Wants to Modify Production Config

1. **Claude Requests to Write File**
   - Claude wants to edit `config/production.yml`
   - `PreToolUse` hook intercepts

2. **Approval Modal Appears**
   Shows:
   - Tool: `Write`
   - File: `config/production.yml`
   - Content: Preview of changes
   - Risk: High (production config)

3. **Review Request**
   - Read the proposed changes carefully
   - Check if changes are appropriate
   - Verify no secrets or sensitive data

4. **Options**

   **Approve**
   - Changes are safe and correct
   - Click "Approve" button
   - Claude proceeds with write

   **Modify**
   - Changes need adjustment
   - Click "Modify" button
   - Edit content in modal
   - Click "Approve" to proceed with modified content

   **Deny**
   - Changes are incorrect or unsafe
   - Click "Deny" button
   - Claude stops operation
   - Explain issue to Claude

   **Always Allow**
   - Trust Claude for this file
   - Check "Always allow writes to this file"
   - Click "Approve"
   - Future writes to this file auto-approved

5. **Continue Development**
   - Claude proceeds based on your decision
   - Approval decision logged for audit
   - Task continues

### Approval Configuration

Customize which operations require approval:

1. **Settings → Approvals**
2. **Toggle Tools**
   - Edit: Require approval for file edits
   - Write: Require approval for new files
   - Bash: Require approval for shell commands
   - NotebookEdit: Require approval for notebook changes
3. **Set Timeouts**
   - How long to wait for approval (default 120s)
4. **Configure Auto-Allow**
   - Patterns that never require approval
   - Example: `*.test.js` files always allowed

## Multi-Branch Development

Work on multiple features simultaneously across branches.

### Scenario: Parallel Feature Development

1. **Branch A: Add Search Feature**

   ```bash
   git checkout -b feature/search
   claude "Implement search functionality"
   ```

   - Task created on "feature/search" branch
   - Work tracked separately

2. **Switch to Branch B: Fix Bug**

   ```bash
   git checkout -b fix/authentication-bug
   claude "Fix session expiration bug"
   ```

   - New task created on "fix/authentication-bug" branch

3. **View Tasks by Branch**
   - Use branch filter in Kanban board
   - See only tasks for selected branch
   - Or view all branches together

4. **Switch Context**
   - Change branches in terminal
   - Tasks automatically associated with correct branch
   - Continue work on any branch

5. **Merge Workflow**
   - Complete tasks on feature branch
   - Review all diffs
   - Merge to main:
   ```bash
   git checkout main
   git merge feature/search
   ```

   - Tasks remain associated with original branch

### Best Practices

- **Use Descriptive Branch Names** - Easy filtering
- **One Epic per Branch** - Link epic to branch
- **Review Before Merge** - Check all tasks on branch
- **Clean Up** - Archive or delete tasks after merge

## Token Budget Management

Monitor and optimize Claude API costs.

### Setting a Budget

1. **Define Monthly Budget**
   - Example: $100/month for API costs
   - Track spending throughout month

2. **Monitor Consumption**
   - Check token usage on task cards
   - View consumption reports
   - Compare against budget

### Daily Workflow

1. **Morning Check**
   - Open consumption report
   - Review yesterday's usage
   - Identify expensive tasks

2. **During Development**
   - Monitor task costs in real-time
   - Badge on task card shows cost
   - Adjust prompts if costs too high

3. **End of Day Review**
   - Total daily consumption
   - Compare to daily budget ($100/30 ≈ $3.33/day)
   - Identify trends

### Optimization Strategies

**Reduce Input Tokens**

- Keep prompts concise
- Avoid including unnecessary context
- Use Claude's memory features

**Use Cache Effectively**

- Let Claude cache common patterns
- Reuse conversations when possible
- Don't restart sessions unnecessarily

**Choose Right Model**

- Use Haiku for simple tasks
- Use Sonnet for standard tasks
- Use Opus only for complex reasoning

**Monitor Expensive Patterns**

- Identify prompts with high costs
- Refine prompting strategy
- Break large tasks into smaller ones

### Budget Alerts

Set up notifications:

1. **Settings → Notifications**
2. **Enable Budget Alerts**
3. **Set Thresholds**
   - 50% of budget
   - 75% of budget
   - 90% of budget
4. **Receive Alerts**
   - Desktop notification
   - Email (optional)

## JIRA Integration Workflow

Link Kintsugi tasks to JIRA tickets for project management.

### Initial Setup

1. **Configure JIRA**
   - Settings → JIRA
   - Enter JIRA URL and credentials
   - Test connection

2. **Verify Access**
   - Ensure you have access to projects
   - API token must have read permissions

### Workflow: Building from JIRA Backlog

1. **Review JIRA Sprint**
   - Open JIRA board in browser
   - Identify ticket to work on
   - Note ticket key (e.g., "PROJ-123")

2. **Start Work with Claude**

   ```bash
   claude "PROJ-123: Implement user profile page"
   ```

   - Include ticket key in prompt
   - Kintsugi auto-detects and links

3. **Task Created with Link**
   - Task appears on board
   - JIRA badge shows ticket key
   - Click to open ticket in JIRA

4. **View JIRA Details in Kintsugi**
   - Click task card
   - JIRA section shows:
     - Ticket status
     - Assignee
     - Priority
     - Description

5. **Work on Task**
   - Complete development as normal
   - Task tracks progress in Kintsugi
   - JIRA status remains separate

6. **Update JIRA Manually**
   - When task done in Kintsugi
   - Update JIRA ticket status manually
   - Add comment with link to commits

### Best Practices

- **Include Ticket Key in Prompt** - Automatic linking
- **One Task per Ticket** - Clear mapping
- **Sync Status** - Keep JIRA updated
- **Add Work Logs** - Link commits in JIRA comments

## Team Collaboration Workflow

Use Kintsugi in a team environment.

### Scenario: Distributed Team

1. **Shared SonarQube Server**
   - All team members connect to same SonarQube instance
   - Use shared quality profiles
   - Consistent code standards

2. **Shared JIRA Project**
   - All tasks linked to JIRA tickets
   - Team visibility into work
   - Centralized project management

3. **Individual Development**
   - Each developer has own Kintsugi instance
   - Tasks tracked locally
   - Independent terminal and workflow

4. **Code Review**
   - Developer completes task
   - Pushes branch to remote
   - Creates pull request

5. **Reviewer Uses Kintsugi**
   - Reviewer clones branch
   - Views task in their Kintsugi
   - Reviews diff and SonarQube for IDE issues
   - Checks token usage (cost efficiency)

6. **Approval and Merge**
   - Reviewer approves in git
   - Branch merged to main
   - JIRA ticket closed

### Team Best Practices

**Standards**

- **Naming Conventions** - Consistent epic and task names
- **Branch Strategy** - Agreed git workflow
- **Quality Rules** - Shared SonarQube profiles
- **Documentation** - Use task comments

**Communication**

- **Daily Standups** - Reference Kintsugi task IDs
- **Status Updates** - Update JIRA with Kintsugi progress
- **Code Reviews** - Share diffs from Kintsugi
- **Retrospectives** - Review token usage patterns

**Shared Configuration**

- **SonarQube URL** - Team server
- **JIRA Instance** - Team instance
- **Quality Profiles** - Synchronized
- **Approval Rules** - Consistent settings

## Onboarding New Projects

Add a new project to Kintsugi.

### Steps

1. **Clone Repository** (if not local)
   - Use Kintsugi's built-in clone feature
   - Or clone manually:

   ```bash
   cd ~/projects
   git clone https://github.com/company/new-project.git
   ```

2. **Add to Kintsugi**
   - Settings → Repositories
   - Click "Add Repository"
   - Select directory or let Kintsugi scan

3. **Configure Project**
   - Set default branch
   - Configure SonarQube binding (if applicable)
   - Link JIRA project (if applicable)

4. **First Task**
   - Open terminal
   - Navigate to project

   ```bash
   cd ~/projects/new-project
   ```

   - Start work:

   ```bash
   claude "Analyze the codebase structure"
   ```

5. **Explore and Document**
   - Use Claude to understand codebase
   - Tasks capture exploration
   - Create epic "Onboarding - Project Name"

### Project Templates

For common project types, create templates:

1. **Create Template Epic**
   - "Onboarding Checklist"
   - Standard tasks for new projects

2. **Clone Template**
   - Duplicate epic for each new project
   - Customize as needed

3. **Example Tasks**
   - "Understand project architecture"
   - "Review dependency versions"
   - "Setup local development environment"
   - "Run test suite"
   - "Identify technical debt"

## Troubleshooting Development Issues

Use Kintsugi to debug and fix issues systematically.

### Workflow

1. **Identify Issue**
   - Bug report from user
   - Test failure
   - Production error

2. **Create Investigation Task**

   ```bash
   claude "Investigate why user login is failing on mobile"
   ```

3. **Claude Investigates**
   - Reads relevant files
   - Analyzes logs
   - Identifies root cause

4. **Review Findings**
   - Check diff for files Claude examined
   - Review SonarQube for IDE issues
   - Check task comments

5. **Create Fix Task**

   ```bash
   claude "Fix the mobile login bug by adjusting viewport handling"
   ```

6. **Link Tasks**
   - Link investigation and fix tasks
   - Or group under "Bug Fix" epic

7. **Verify Fix**
   - Review changes in diff
   - Check SonarQube for IDE issues resolved
   - Test manually

8. **Document**
   - Add comment explaining root cause
   - Note prevention strategy
   - Link JIRA ticket if applicable

## Next Steps

Explore these resources to enhance your workflow:

- **[Features Overview](features.md)** - Learn all features in detail
- **[Configuration Guide](configuration.md)** - Customize your setup
- **[Integrations](integrations.md)** - Connect external tools
- **[Troubleshooting](troubleshooting.md)** - Solve common problems
