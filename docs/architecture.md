# System Architecture

This document provides a technical overview of Kintsugi's architecture, components, and data flow. Understanding the architecture helps with troubleshooting, customization, and contributing to the project.

## Architecture Overview

Kintsugi follows a **local-first architecture** with five main components working together to provide a seamless development experience.

```
┌─────────────────────────────────────────────────────────────┐
│                     Desktop Application                       │
│  (Electron + React + TypeScript)                             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐            │
│  │ Main       │  │ Renderer   │  │ Preload    │            │
│  │ Process    │◄─┤ Process    │◄─┤ Scripts    │            │
│  └────┬───────┘  └────┬───────┘  └────────────┘            │
│       │               │                                       │
└───────┼───────────────┼───────────────────────────────────────┘
        │               │
        │ IPC           │ HTTP + WebSocket
        │               │
┌───────▼───────────────▼─────────────────────────────────────┐
│              Local Backend (Port 63421)                      │
│  (Node.js + Express + SQLite + Socket.IO)                   │
│  ┌─────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ REST    │  │ WebSocket│  │ SQLite   │  │ Services │    │
│  │ API     │  │ Server   │  │ Database │  │          │    │
│  └─────────┘  └──────────┘  └──────────┘  └──────────┘    │
└─────────────────────────────────────────────────────────────┘
        │                                   │
        │ HTTP                              │ HTTP
        │                                   │
┌───────▼──────────────────┐    ┌───────────▼─────────────────┐
│ SonarLint Bridge         │    │ Kintsugi CLI Plugin         │
│ (Port 8765)              │    │ (npm global package)        │
│ (Java + SonarLint RPC)   │    │ (Node.js + TypeScript)      │
│                          │    │                             │
│ - Code Analysis          │    │ - Hook Scripts              │
│ - Quality Profiles       │    │ - Snapshot Management       │
│ - Issue Detection        │    │ - Diff Generation           │
└──────────────────────────┘    └─────────────┬───────────────┘
                                              │
                                              │ Hooks
                                              │
                                    ┌─────────▼──────────┐
                                    │   Claude Code CLI   │
                                    │                     │
                                    │ - AI Interactions   │
                                    │ - Tool Execution    │
                                    │ - File Operations   │
                                    └─────────────────────┘
```

## Components

### 1. Desktop Application

**Technology Stack**
- Electron 39.3.0
- React 18.3.1
- TypeScript
- Vite (build tool)
- TailwindCSS (styling)

**Main Process**

The main process manages the application lifecycle and native operations.

**Responsibilities:**
- Window management
- Native menu creation
- System tray integration
- Backend process spawning
- SonarLint Bridge spawning
- IPC communication with renderer
- File system operations
- Shell execution

**Key Files:**
- `main/index.ts` - Entry point and window creation
- `main/services/backend-manager.ts` - Backend lifecycle
- `main/services/sonarlint-manager.ts` - Bridge lifecycle
- `main/handlers/*.ts` - IPC request handlers (20+ modules)

**Renderer Process**

The renderer process runs the React UI.

**Responsibilities:**
- User interface rendering
- State management
- API communication
- WebSocket client
- Terminal emulation
- Code editing
- Diff visualization

**Key Directories:**
- `renderer/src/components/` - React components (23 categories)
- `renderer/src/hooks/` - Custom React hooks (35+)
- `renderer/src/contexts/` - Context providers (18+)
- `renderer/src/services/` - API clients and utilities

**Preload Scripts**

Secure bridge between main and renderer processes.

**Security Model:**
- Context isolation enabled
- No direct Node.js access in renderer
- IPC through exposed APIs only
- Principle of least privilege

### 2. Local Backend

**Technology Stack**
- Node.js 22+
- Express
- TypeScript
- sql.js (SQLite in JavaScript)
- Socket.IO (WebSockets)

**Architecture**

The backend is a standalone Node.js process spawned by the desktop app.

**Responsibilities:**
- REST API server
- WebSocket event broadcasting
- SQLite database management
- Token consumption calculation
- SonarLint communication
- Hook request processing
- Session monitoring

**API Routes** (14 modules)

| Route | Purpose |
|-------|---------|
| `/api/tasks` | Task CRUD and queries |
| `/api/epics` | Epic management |
| `/api/sessions` | Session tracking |
| `/api/hooks/*` | Hook event processing |
| `/api/approval-requests` | Tool approvals |
| `/api/sonarlint/*` | Code analysis |
| `/api/sonarqube/*` | SonarQube integration |
| `/api/jira/*` | JIRA integration |
| `/api/user-repositories` | Repository management |
| `/api/onboarding` | Setup wizards |
| `/api/telemetry` | Event logging |
| `/health` | Health checks |

**Database Schema**

SQLite database with 12+ tables:

**Core Tables:**
- `tasks` - Task records with metadata
- `epics` - Epic collections
- `terminal_sessions` - Terminal tracking

**Analysis Tables:**
- `sonarlint_analyses` - Code analysis results
- `task_consumption` - Token usage

**Integration Tables:**
- `sonarqube_credentials` - SQ connection info
- `sonarqube_bindings` - Project bindings
- `jira_credentials` - JIRA auth
- `jira_tickets` - Cached ticket data

**Workflow Tables:**
- `approval_requests` - Tool approvals
- `user_repositories` - Repository metadata
- `telemetry_events` - Analytics

**Services**

Background services for async operations:

**Consumption Service**
- Parses Claude CLI transcript logs
- Extracts token usage from API responses
- Calculates costs using current pricing
- Updates task_consumption table
- Runs every 30 seconds

**Session Watcher**
- Monitors Claude Code processes
- Detects session start/end
- Updates session status
- Spawned as separate Node.js process

**Job Queue**
- Processes async tasks
- Handles long-running operations
- Retry logic for failures

**SonarQube Sync Service**
- Syncs quality profiles
- Downloads rule definitions
- Updates binding configuration

**Process Management**

Backend runs as detached process:
- PID stored in `~/.kintsugi/local-backend.pid`
- Logs to `~/.kintsugi/local-backend.log`
- Graceful shutdown on SIGTERM
- Auto-restart on crash

### 3. SonarLint Bridge

**Technology Stack**
- Java 17+
- Javalin (HTTP server)
- SonarLint RPC Client
- Gson (JSON)
- Maven (build)

**Architecture**

Java service wrapping SonarLint analysis engine.

**Responsibilities:**
- HTTP API for code analysis
- Language analyzer management
- SonarQube connected mode
- Quality profile synchronization
- Analyzer downloads from Maven Central

**API Endpoints**

| Endpoint | Purpose |
|----------|---------|
| `POST /analyze` | Analyze single file |
| `POST /analyze-batch` | Analyze multiple files |
| `POST /sync-quality-profile` | Sync from SonarQube |
| `GET /supported-languages` | List languages |
| `GET /health` | Health check |

**Analysis Flow**

1. **Request Received**
   - File path and content
   - Language (auto-detected if not specified)
   - Quality profile (if SonarQube connected)

2. **Analyzer Selection**
   - Determine language from extension
   - Load appropriate analyzer
   - Download if not cached

3. **Analysis Execution**
   - Parse file syntax
   - Apply quality rules
   - Detect issues
   - Timeout after 120 seconds

4. **Results Returned**
   - Issues with line/column
   - Severity and type
   - Rule descriptions
   - Quick fixes (if available)

**Connected Mode**

When bound to SonarQube:
- Uses server-defined quality profiles
- Applies same rules as CI
- Maintains consistency with team

**Performance**

- Analyzer caching for speed
- Lazy loading of analyzers
- Parallel analysis support
- Configurable timeouts

**Process Management**

Bridge runs as detached Java process:
- PID tracked by desktop app
- Logs written to stdout/stderr
- Auto-restart on crash
- Graceful shutdown

### 4. Kintsugi CLI Plugin

**Technology Stack**
- Node.js
- TypeScript
- yargs (CLI parsing)
- simple-git (git operations)

**Architecture**

npm global package with hook scripts.

**Installation**
```bash
npm install -g @kintsugi/plugin
```

**Hook Scripts**

Eight hook types registered with Claude Code:

| Hook | Trigger | Action |
|------|---------|--------|
| `UserPromptSubmit` | Prompt sent to Claude | Create task |
| `PreToolUse` | Before tool execution | Capture file snapshot |
| `PostToolUse` | After tool execution | Record tool use |
| `Stop` | Claude pauses | Generate diff |
| `SessionStart` | Session begins | Track session |
| `SessionEnd` | Session ends | Move tasks to review |
| `Notification` | Status message | Log notification |
| `PermissionRequest` | Tool needs approval | Request approval |

**Hook Execution Flow**

1. **Claude Fires Hook**
   - Hook script executed
   - JSON payload passed via stdin
   - Environment variables available

2. **Script Processes Event**
   - Parse payload
   - Gather context (git info, etc.)
   - Make API call to backend

3. **Backend Processes Request**
   - Create/update records
   - Broadcast WebSocket events
   - Return response

4. **Script Returns**
   - Success/failure status
   - Optional data for Claude
   - stdout/stderr logged

**Snapshot Management**

Before file edits:
- Original content captured
- Stored at `~/.kintsugi/task-snapshots/tasks/{taskId}/{filePath}.before`
- After edits, `.after` file created
- Diff generated by comparing

**Diff Generation**

Uses git diff algorithm:
- Unified diff format
- Context lines (default 3)
- Line numbers included
- Binary files detected

**Git Integration**

Utilities for git operations:
- Detect repository root
- Get current branch
- Determine remote platform (GitHub/GitLab/Bitbucket)
- Read commit info

### 5. External Integrations

**SonarQube**

Connected mode integration:
- Token-based authentication
- REST API v9.9+
- Quality profile sync
- Project binding

**JIRA**

Read-only ticket integration:
- Token-based auth (Atlassian API)
- Ticket metadata fetching
- Comment reading (optional)
- 5-minute cache

**GitHub**

Pull request integration:
- Personal access token
- REST API v3
- PR metadata
- Comment support

## Data Flow

### Task Creation Flow

```
1. User submits prompt to Claude
   │
   ├─ UserPromptSubmit hook fires
   │
2. Plugin captures context
   │
   ├─ Get git info (repo, branch)
   ├─ Extract prompt text
   │
3. HTTP POST to /api/hooks/prompt-submit
   │
   ├─ Backend receives request
   ├─ Create task record in SQLite
   ├─ Generate task ID
   │
4. WebSocket broadcast: task:created
   │
   ├─ All connected clients receive
   ├─ Desktop UI updates Kanban board
   │
5. Task card appears in "To Do" column
```

### File Edit Flow

```
1. Claude calls Edit tool
   │
   ├─ PreToolUse hook fires
   │
2. Plugin captures original file
   │
   ├─ Read current content
   ├─ Save as .before snapshot
   │
3. HTTP POST to /api/hooks/pre-tool-use
   │
   ├─ Backend logs tool use
   │
4. Claude executes edit
   │
   ├─ File content modified
   │
5. PostToolUse hook fires
   │
   ├─ Backend updates task status to "in_progress"
   │
6. WebSocket broadcast: task:updated
   │
   ├─ Desktop UI moves card to "In Progress"
   │
7. Optional: SonarLint analysis triggered
   │
   ├─ HTTP POST to bridge /analyze
   ├─ Results stored in database
   ├─ WebSocket broadcast: sonarlint:analysis-completed
   ├─ Issues shown in UI
```

### Diff Generation Flow

```
1. Claude stops (awaits input)
   │
   ├─ Stop hook fires
   │
2. Plugin generates diff
   │
   ├─ Find .before and .after snapshots
   ├─ Run git diff algorithm
   ├─ Create unified diff
   │
3. HTTP POST to /api/hooks/stop
   │
   ├─ Backend stores diff in savedDiff field
   ├─ Update task status to "awaiting_input"
   │
4. WebSocket broadcast: task:updated
   │
   ├─ Desktop UI moves card to "Awaiting Input"
   ├─ "View Diff" button enabled
   │
5. User clicks "View Diff"
   │
   ├─ Fetch diff from backend
   ├─ Parse unified diff format
   ├─ Render side-by-side view
```

### Token Consumption Flow

```
1. Consumption service runs (every 30s)
   │
   ├─ Scan ~/.claude/logs/ directory
   │
2. Find transcript files
   │
   ├─ Parse JSON lines
   ├─ Extract API responses
   │
3. Find usage data
   │
   ├─ input_tokens
   ├─ output_tokens
   ├─ cache_creation_input_tokens
   ├─ cache_read_input_tokens
   │
4. Calculate costs
   │
   ├─ Apply pricing per model
   ├─ Sum token categories
   │
5. Match to sessions/tasks
   │
   ├─ Correlate by session ID
   ├─ Attribute to tasks
   │
6. Update task_consumption table
   │
   ├─ Store breakdown
   │
7. WebSocket broadcast: consumption:updated
   │
   ├─ UI updates cost badges
```

### Approval Flow

```
1. Claude requests dangerous operation
   │
   ├─ PermissionRequest hook fires
   │
2. HTTP POST to /api/approval-requests
   │
   ├─ Backend creates approval record
   ├─ Set status: pending
   │
3. WebSocket broadcast: approval:created
   │
   ├─ Desktop UI shows modal
   │
4. Hook polls for decision
   │
   ├─ GET /api/approval-requests/{id}
   ├─ Every 500ms
   ├─ Timeout: 120s
   │
5. User decides
   │
   ├─ Approve, deny, or modify
   │
6. HTTP PATCH /api/approval-requests/{id}
   │
   ├─ Update status and decision
   │
7. Hook receives decision
   │
   ├─ Return to Claude
   ├─ Claude proceeds or stops
   │
8. Decision logged for audit
```

## Communication Protocols

### IPC (Inter-Process Communication)

Between Electron main and renderer processes.

**Security:**
- Context isolation enabled
- No direct Node.js access in renderer
- Whitelist of allowed channels
- Input validation on all messages

**Pattern:**
```typescript
// Renderer
const result = await window.api.invoke('channel-name', arg1, arg2)

// Main
ipcMain.handle('channel-name', async (event, arg1, arg2) => {
  return result
})
```

### HTTP REST API

Between desktop/plugin and backend.

**Authentication:**
- Bearer token in Authorization header
- Token auto-generated on first run
- Stored in `~/.kintsugi/config.json`

**Request Format:**
```http
POST /api/tasks HTTP/1.1
Host: localhost:63421
Authorization: Bearer {token}
Content-Type: application/json

{
  "prompt": "Add authentication",
  "branch": "feature/auth",
  "repository": "/path/to/repo"
}
```

**Response Format:**
```json
{
  "success": true,
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "status": "todo",
    ...
  }
}
```

### WebSocket

Real-time updates via Socket.IO.

**Connection:**
```typescript
const socket = io('http://localhost:63421', {
  auth: { token: authToken }
})
```

**Event Format:**
```typescript
// Server broadcasts
socket.emit('task:created', { task: {...} })

// Client listens
socket.on('task:created', (data) => {
  // Update UI
})
```

**Events:**
- `task:created`
- `task:updated`
- `task:deleted`
- `epic:created`
- `epic:updated`
- `approval:created`
- `approval:updated`
- `sonarlint:analysis-completed`
- `consumption:updated`

## Security Considerations

### Local-First Architecture

- **No cloud storage** - All data stays local
- **No external dependencies** - Works offline
- **User-controlled** - Full data ownership

### Authentication

- **Token-based** - Auto-generated secure tokens
- **Localhost only** - Backend bound to 127.0.0.1
- **No public exposure** - Firewall not required

### File System Access

- **Controlled** - Only configured directories
- **User permissions** - Respects file system permissions
- **No elevation** - Never requires sudo/admin

### Process Isolation

- **Separate processes** - Backend, bridge, watcher
- **Crash isolation** - One crash doesn't affect others
- **Resource limits** - Configurable memory/CPU

## Performance Optimization

### Database

- **SQLite** - Fast, embedded database
- **Indexes** - On frequently queried fields
- **Connection pooling** - Reuse connections
- **Batch operations** - Reduce round trips

### WebSocket

- **Event batching** - Combine rapid updates
- **Selective broadcast** - Only to interested clients
- **Compression** - Reduce bandwidth

### Frontend

- **React Query** - Smart caching and updates
- **Virtualization** - Render only visible items
- **Code splitting** - Load features on demand
- **Lazy loading** - Defer non-critical imports

### File Operations

- **Streaming** - Don't load entire files
- **Caching** - Remember read files
- **Debouncing** - Reduce rapid operations

## Extensibility

### Plugin Architecture

Future plugin support planned:
- Custom hook actions
- UI extensions
- Analysis engines
- Integration connectors

### API Extensibility

REST API versioned for stability:
- `/api/v1/*` - Current stable API
- Backward compatibility maintained
- Deprecation notices for changes

## Next Steps

- **[Getting Started](getting-started.md)** - Set up Kintsugi
- **[Configuration](configuration.md)** - Customize architecture
- **[Troubleshooting](troubleshooting.md)** - Debug architectural issues
- **[Data & Privacy](data-privacy.md)** - Understand data handling
