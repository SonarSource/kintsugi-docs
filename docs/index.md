# Kintsugi

**Automated Development Environment for Claude Code**

Kintsugi is a comprehensive task management and code analysis platform that seamlessly integrates with Claude Code CLI. It automatically tracks your development workflows in real-time, providing visual task management, integrated terminal emulation, code quality analysis, and token usage tracking—all while capturing your Claude-assisted development sessions.

---

## What is Kintsugi?

Kintsugi transforms Claude Code from a CLI tool into a fully-featured development environment by providing:

- **Automatic Task Tracking** - Tasks are created automatically as you work with Claude
- **Visual Kanban Board** - Organize and track work across five workflow stages
- **Integrated Terminal** - Execute Claude commands directly in the app
- **Code Quality Analysis** - Real-time SonarLint analysis with SonarQube integration
- **Token Usage Tracking** - Monitor API costs per task, epic, or entire sessions
- **File Diff Viewer** - Review all changes before committing
- **Tool Approval Workflow** - Control dangerous operations with approval modals
- **Epic-Based Organization** - Group related tasks for large features
- **JIRA Integration** - Link tasks to external project tracking
- **Real-time Synchronization** - Multiple instances stay synchronized via WebSockets

---

## Quick Start

### Prerequisites

- **Node.js** 22+ and npm 10+
- **Claude CLI** installed and configured
- **Java** 17+ (for SonarLint code analysis)
- **Git** for repository management

### Installation

=== "macOS"

    1. Download the `.dmg` installer
    2. Drag Kintsugi to Applications folder
    3. Launch from Applications

=== "Windows"

    1. Download the `.exe` installer
    2. Run the installer
    3. Launch from Start Menu

=== "Linux"

    ```bash
    # AppImage
    chmod +x Kintsugi-x.y.z.AppImage
    ./Kintsugi-x.y.z.AppImage

    # Debian/Ubuntu
    sudo dpkg -i Kintsugi-x.y.z.deb
    ```

### First Steps

1. **Open the Terminal** in Kintsugi
2. **Navigate to your project** directory
3. **Start Claude Code** with your first prompt
4. **Watch your task appear** automatically on the Kanban board
5. **Track your progress** as Claude works through your request

[Get Started →](getting-started.md){ .md-button .md-button--primary }
[View Features →](features.md){ .md-button }

---

## Key Features at a Glance

### Automatic Task Management
Tasks are created automatically when you submit prompts to Claude Code. Each task captures:

- The prompt text
- Git branch and repository context
- File changes and diffs
- Token usage and costs
- Complete audit trail

### Integrated Development Environment
- **Terminal Emulation** - Full-featured terminal built into the app
- **Code Editor** - View files with syntax highlighting
- **Diff Viewer** - Side-by-side comparison of changes
- **File Browser** - Navigate your project structure

### Code Quality Analysis
- **SonarLint Integration** - Real-time code analysis as Claude makes changes
- **SonarQube Connected Mode** - Sync quality profiles from your SonarQube instance
- **Issue Detection** - Find bugs, vulnerabilities, and code smells
- **Inline Annotations** - Issues displayed directly in the diff viewer

### Token Usage Tracking
- Automatic calculation of token consumption from Claude CLI transcripts
- Per-model breakdowns (input/output, cache creation/read)
- Cost tracking in USD
- Reports by task, epic, or all work

### Local-First Architecture
All sensitive data stays on your machine:

- SQLite database at `~/.kintsugi/local.db`
- File snapshots in `~/.kintsugi/task-snapshots/`
- No cloud storage required
- Optional telemetry (can be disabled)

---

## Technology Stack

<div class="grid cards" markdown>

-   :material-electron-framework:{ .lg .middle } **Desktop App**

    ---

    Electron, React, TypeScript, TailwindCSS

-   :material-nodejs:{ .lg .middle } **Local Backend**

    ---

    Node.js, Express, SQLite, Socket.IO

-   :material-language-java:{ .lg .middle } **Code Analysis**

    ---

    Java, SonarLint RPC Client

-   :material-console:{ .lg .middle } **CLI Plugin**

    ---

    Node.js, TypeScript

-   :material-terminal:{ .lg .middle } **Terminal**

    ---

    xterm.js, node-pty

-   :material-webpack:{ .lg .middle } **Build Tools**

    ---

    Vite, electron-builder

</div>

---

## Community & Support

- **Issues**: [GitHub Issues](https://github.com/your-org/kintsugi/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/kintsugi/discussions)
- **Documentation**: Full Documentation (you're here!)

---

## License

MIT License - see [LICENSE](https://github.com/your-org/kintsugi/blob/main/LICENSE) for details

## Security

For security concerns, please see [SECURITY.md](https://github.com/your-org/kintsugi/blob/main/SECURITY.md)
