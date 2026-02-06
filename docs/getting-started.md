# Getting Started with Kintsugi

This guide will walk you through installing Kintsugi, setting up the CLI plugin, and completing your first Claude-assisted development session.

## Prerequisites

Before installing Kintsugi, ensure you have the following:

### Required

- **Node.js 22+** and **npm 10+**
  - Check version: `node --version && npm --version`
  - Download from: [nodejs.org](https://nodejs.org/)

- **Claude Code CLI**
  - Must be installed and configured with your API key
  - Verify installation: `claude --version`
  - If not installed, visit: [Claude Code documentation](https://docs.anthropic.com/claude-code)

- **Git**
  - Check version: `git --version`
  - Required for repository management and version control

### Optional but Recommended

- **Java 17+**
  - Required for SonarQube for IDE code analysis features
  - Check version: `java --version`
  - Download from: [OpenJDK](https://openjdk.org/) or [Oracle JDK](https://www.oracle.com/java/technologies/downloads/)

- **jq**
  - JSON processing tool used by CLI plugin
  - macOS: `brew install jq`
  - Linux: `apt-get install jq` or `yum install jq`
  - Windows: Download from [stedolan.github.io/jq](https://stedolan.github.io/jq/)

- **Python 3.10+**
  - Required for building native Node.js modules
  - Check version: `python3 --version`

## Installation

### macOS

1. **Download the installer**
   - Download the `.dmg` file from the releases page
   - Open the downloaded `.dmg` file

2. **Install the application**
   - Drag the Kintsugi icon to the Applications folder
   - Wait for the copy to complete

3. **Launch Kintsugi**
   - Open Kintsugi from Applications or Spotlight
   - If prompted about an unidentified developer:
     - Go to System Preferences → Security & Privacy
     - Click "Open Anyway" next to the Kintsugi message

### Windows

1. **Download the installer**
   - Download the `.exe` installer from the releases page

2. **Run the installer**
   - Double-click the downloaded file
   - Follow the installation wizard
   - Choose installation directory (default: `C:\Program Files\Kintsugi`)

3. **Launch Kintsugi**
   - Find Kintsugi in the Start Menu
   - Or double-click the desktop shortcut if created

### Linux

#### AppImage (Universal)

1. **Download the AppImage**

   ```bash
   wget <release-url>/Kintsugi-x.y.z.AppImage
   ```

2. **Make it executable**

   ```bash
   chmod +x Kintsugi-x.y.z.AppImage
   ```

3. **Run the application**
   ```bash
   ./Kintsugi-x.y.z.AppImage
   ```

#### Debian/Ubuntu (.deb)

```bash
sudo dpkg -i Kintsugi-x.y.z.deb
sudo apt-get install -f
```

#### Red Hat/Fedora (.rpm)

```bash
sudo rpm -i Kintsugi-x.y.z.rpm
```

## First Launch Setup

When you launch Kintsugi for the first time, you'll go through a guided onboarding process.

### Step 1: Backend Initialization

Kintsugi will automatically:

- Start the local backend server on port 63421
- Create configuration directory at `~/.kintsugi/`
- Initialize SQLite database at `~/.kintsugi/local.db`
- Start SonarQube for IDE Bridge on port 8765 (if Java is available)

You'll see a startup screen while these services initialize.

### Step 2: CLI Plugin Installation

The plugin enables automatic task tracking by registering hooks with Claude Code.

1. **Installation Prompt**
   - Kintsugi will detect if the plugin is not installed
   - Click "Install Plugin" when prompted

2. **What Gets Installed**
   - The `@kintsugi/plugin` npm package is installed globally
   - Hook scripts are registered in `~/.claude/settings.json`
   - Configuration file is created at `~/.kintsugi/config.json`

3. **Manual Installation** (if needed)

   ```bash
   npm install -g @kintsugi/plugin
   kintsugi register-hooks
   ```

4. **Verify Installation**
   - Open `~/.claude/settings.json`
   - You should see hook entries like:
   ```json
   {
     "hooks": {
       "UserPromptSubmit": "/path/to/plugin/hooks/on-prompt-submit.js",
       "PreToolUse": "/path/to/plugin/hooks/on-tool-use.js",
       ...
     }
   }
   ```

### Step 3: Repository Selection

1. **Select Your Project**
   - Kintsugi will scan your home directory for Git repositories
   - Choose a repository to work with
   - Or clone a new repository using the built-in clone dialog

2. **Repository is Saved**
   - Selected repositories are saved to `~/.kintsugi/desktop-config.json`
   - You can change repositories anytime from the settings

### Step 4: Optional Integrations

You can configure these later, but the onboarding wizard gives you the option:

- **SonarQube** - For team-wide quality profiles
- **JIRA** - For ticket tracking
- **GitHub** - For pull request management

Click "Skip" to configure these later in Settings.

## Your First Task

Now you're ready to create your first task with Claude!

### 1. Open the Terminal

- Click the "Terminal" button in the bottom panel
- Or use the keyboard shortcut (Ctrl/Cmd + `)
- A full terminal will open in the app

### 2. Navigate to Your Project

```bash
cd /path/to/your/project
```

The terminal is a real shell session - you can run any commands you normally would.

### 3. Start Claude Code

Submit your first prompt to Claude:

```bash
claude "Add error handling to the API routes"
```

Or use interactive mode:

```bash
claude
> Add error handling to the API routes
```

### 4. Watch the Magic Happen

As soon as you submit the prompt:

1. **Task Created Automatically**
   - A new card appears in the "To Do" column
   - Task captures your prompt, git branch, and repository

2. **Status Updates Automatically**
   - When Claude starts editing files → moves to "In Progress"
   - When Claude stops and waits → moves to "Awaiting Input"
   - When session ends → moves to "Ready for Review"

3. **File Changes Tracked**
   - Original file content is captured before edits
   - Diffs are generated automatically
   - Click "View Diff" to see all changes

4. **Token Usage Calculated**
   - Consumption is tracked per task
   - Cost is calculated based on model usage
   - View details in the task card

### 5. Review and Complete

Once Claude finishes:

1. **Click "View Diff"** to review changes
2. **Check SonarQube for IDE Issues** (if enabled)
3. **Add Comments** if needed
4. **Drag to "Done"** when satisfied
5. **Commit your changes** in the terminal

## Understanding the Interface

### Kanban Board

The board has 5 columns representing task workflow:

- **To Do** - Tasks not yet started
- **In Progress** - Claude is actively working
- **Awaiting Input** - Waiting for your response
- **Ready for Review** - Changes complete, ready for your review
- **Done** - Reviewed and accepted

### Task Cards

Each task card shows:

- Prompt text
- Git branch and repository
- Status and timestamps
- Token usage and cost
- Action buttons (View Diff, Delete, etc.)

### Terminal Panel

The integrated terminal:

- Runs your system shell (bash/zsh/PowerShell)
- Supports all standard terminal features
- Commands are linked to active tasks
- Multiple terminal tabs supported

### Bottom Panel Tabs

- **Terminal** - Command line interface
- **Diff Viewer** - Side-by-side file comparison
- **Code Editor** - File viewer with syntax highlighting
- **SonarQube for IDE** - Code quality issues

## Next Steps

Now that you have Kintsugi running, explore these features:

1. **[Configure Integrations](integrations.md)** - Set up SonarQube, JIRA, or GitHub
2. **[Learn Workflows](workflows.md)** - Discover common usage patterns
3. **[Customize Settings](configuration.md)** - Tailor Kintsugi to your needs
4. **[Organize with Epics](features.md#epics)** - Group related tasks

## Common Setup Issues

If you encounter problems during setup, see the [Troubleshooting Guide](troubleshooting.md) for solutions to common issues:

- Backend fails to start
- Hooks not registering
- SonarQube for IDE Bridge not starting
- Permission errors
- Port conflicts

## Configuration Files Reference

After setup, you'll find these files in `~/.kintsugi/`:

| File                  | Purpose                              |
| --------------------- | ------------------------------------ |
| `local.db`            | SQLite database with all task data   |
| `config.json`         | Backend URL and authentication token |
| `desktop-config.json` | UI preferences and credentials       |
| `local-backend.log`   | Backend server logs                  |
| `hook-debug.log`      | CLI plugin logs                      |
| `local-backend.pid`   | Backend process ID                   |
| `task-snapshots/`     | Before/after file snapshots          |

## Getting Help

If you run into issues:

1. Check the [Troubleshooting Guide](troubleshooting.md)
2. Review logs in `~/.kintsugi/`
3. Open an issue on GitHub
4. Join the community discussions

Welcome to Kintsugi! You're now ready to supercharge your Claude-assisted development workflow.
