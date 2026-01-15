# VoiceTypr

<<<<<<< HEAD
macOS desktop app for offline voice transcription using Whisper AI. Built with Tauri v2 (Rust backend) and React 19 (TypeScript frontend). Features system-wide hotkey recording, automatic text insertion at cursor, and local model management.
=======
macOS desktop app for offline voice transcription using Whisper AI. Built with Tauri v2 (Rust backend) and React 19 (TypeScript frontend). Features system-wide hotkey recording, automatic text insertion at cursor, local model management, and **remote transcription via network sharing**.

## 🔴 CRITICAL: Beads Viewer & Daemon

This project uses **three essential tools** for issue tracking:

| Tool | Purpose | Command | Source |
|------|---------|---------|--------|
| **Beads CLI (`bd`)** | Issue tracking commands | `bd list`, `bd ready`, etc. | [steveyegge/beads](https://github.com/steveyegge/beads) |
| **Beads Viewer (`bv`)** | Web dashboard showing all issues | `bv --preview-pages bv-site` | [Dicklesworthstone/beads_viewer](https://github.com/Dicklesworthstone/beads_viewer) |
| **Beads Daemon** | Syncs dashboard every 30 seconds | `./beads-watch.sh` (or `.ps1`) | (local script in repo) |

**⚠️ The daemon MUST be running or the dashboard shows stale/wrong data!**

The daemon watches the SQLite database and syncs changes to the JSONL file that the viewer reads. Without it, status updates (like `open → in_progress`) won't appear.

**Dashboard URL:** http://127.0.0.1:9001

## ⚡ Quick Start Checklist

**Do these steps at the START of every session:**

```bash
# 1. Start beads daemon (REQUIRED - syncs dashboard every 30 seconds)
./beads-watch.sh &              # macOS/Linux
# OR: powershell -ExecutionPolicy Bypass -File beads-watch.ps1  # Windows

# 2. Start Beads Viewer dashboard
bv --preview-pages bv-site &    # Opens dashboard at http://127.0.0.1:9001

# 3. Check what's being worked on
bd list --status=in_progress    # See active work
bd ready                        # Find available issues

# 4. Read the prioritized issue list
cat bv-site/README.md
```

**Before starting any issue:**
```bash
bd show <issue-id>                    # Read FULL details + comments
bd update <id> --status=in_progress   # Claim it before working
```

**While working:**
```bash
bd comments add <id> "Progress: ..."  # Add regular updates
```

**After completing:**
```bash
bd comments add <id> "STATUS: READY FOR VERIFICATION - <summary>"
# DO NOT use 'bd close' - wait for user to verify
```
>>>>>>> 535303e (docs: add source repository links to Beads tools table)

## Core Commands

```bash
# Development
pnpm dev              # Frontend only (Vite)
pnpm tauri dev        # Full Tauri app (frontend + Rust)

# Quality checks (run before commits)
pnpm lint             # ESLint
pnpm typecheck        # TypeScript compiler
pnpm test             # Vitest frontend tests
pnpm test:backend     # Rust tests (cd src-tauri && cargo test)
pnpm quality-gate     # All checks in one script

# Build
pnpm build            # Frontend build
pnpm tauri build      # Native .app bundle
```

## Project Layout

```
src/                          # React frontend
├── components/               # UI components
│   ├── ui/                   # shadcn/ui primitives
│   ├── tabs/                 # Tab panel components
│   └── sections/             # Page sections
├── contexts/                 # React context providers
├── hooks/                    # Custom React hooks
├── lib/                      # Shared utilities
├── utils/                    # Helper functions
├── services/                 # External service integrations
├── state/                    # State management (Zustand)
└── test/                     # Integration tests

src-tauri/src/                # Rust backend
├── commands/                 # Tauri command handlers
├── audio/                    # CoreAudio recording
├── whisper/                  # Transcription engine
├── ai/                       # AI model management
├── parakeet/                 # Parakeet sidecar integration
├── state/                    # Backend state management
├── utils/                    # Rust utilities
└── tests/                    # Rust unit tests
```

## Development Patterns

### Frontend
- **Framework**: React 19 with function components + hooks
- **Styling**: Tailwind CSS v4; use `@/*` path alias for imports
- **Components**: shadcn/ui in `src/components/ui/`; extend, don't modify
- **State**: React hooks + Zustand + Tauri events
- **Types**: Strict TypeScript; avoid `any`
- **Tests**: Vitest + React Testing Library; test user behavior, not implementation

### Backend
- **Language**: Rust 2021 edition
- **Framework**: Tauri v2 with async commands
- **Modules**: Commands in `commands/`; domain logic in dedicated modules
- **Style**: Run `cargo fmt` and `cargo clippy` before commits
- **Tests**: Unit tests in `tests/` directory; use `#[tokio::test]` for async

### Communication
- Frontend calls backend via `invoke()` from `@tauri-apps/api`
- Backend emits events via `app.emit()` or `window.emit()`
- Event coordination handled by `EventCoordinator` class

## Git Workflow

- **Commits**: Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`)
- **Pre-commit**: Run `pnpm quality-gate` or individual checks
- **Branches**: Feature branches off `main`
- **Never push** without explicit user instruction

```bash
git status                    # Always check first
git diff                      # Review changes
git add -A && git commit -m "feat: description"
```

## Beads Issue Tracking (Multi-Agent)

This project uses **Beads** for issue tracking across multiple Claude Code agents.

**Source repositories:**
- **beads** (`bd`): https://github.com/steveyegge/beads - Git-backed issue tracker
- **beads_viewer** (`bv`): https://github.com/Dicklesworthstone/beads_viewer - Dashboard UI

### First-Time Setup (Bootstrap)

If `bd` or `bv` commands are not found, install them:

**Install `bd` (Beads CLI):**
```bash
# macOS/Linux (Homebrew)
brew install steveyegge/beads/bd

# Or via curl
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash

# Or via npm
npm install -g @beads/bd
```

**Install `bv` (Beads Viewer):**
```bash
# macOS/Linux
curl -fsSL "https://raw.githubusercontent.com/Dicklesworthstone/beads_viewer/main/install.sh" | bash

# Windows (PowerShell)
irm "https://raw.githubusercontent.com/Dicklesworthstone/beads_viewer/main/install.ps1" | iex
```

**Verify installation:**
```bash
bd --version && bv --version && bd list
```

### Session Startup (REQUIRED - DO THIS FIRST)

**You MUST start the beads watch daemon at the beginning of every session.**

**Detect your platform**, then run the appropriate commands:

#### macOS / Linux
```bash
./beads-watch.sh &
bv --preview-pages bv-site &
```

#### Windows (PowerShell)
```powershell
powershell -ExecutionPolicy Bypass -File beads-watch.ps1
# In a separate terminal:
bv --preview-pages bv-site
```

#### Windows (Git Bash / WSL)
```bash
./beads-watch.sh &
bv --preview-pages bv-site &
```

**Dashboard URL:** http://127.0.0.1:9001

### Beads Watch Daemon Explained

**Watch script files (in project root):**
- `beads-watch.ps1` - Windows PowerShell version
- `beads-watch.sh` - macOS/Linux bash version

**What it does:**
- Runs every 30 seconds
- Compares MD5 hash of `bd export` output vs `.beads/issues.jsonl`
- If different, syncs JSONL and regenerates `bv-site/` dashboard
- Detects ALL changes including status updates (e.g., `open → in_progress`)

**Why it's necessary:**
- `bd` stores in SQLite, `bv` reads from JSONL
- Without daemon, dashboard shows stale/wrong data
- Multiple agents need accurate real-time view of issue states

### Essential Commands

```bash
bd ready                          # Find available work (no blockers)
bd list --status=in_progress      # See what others are working on
bd update <id> --status=in_progress  # Claim work before starting
bd close <id> --reason="..."      # ONLY after user confirms completion
```

### Manual Sync (If Daemon Not Running)

#### macOS / Linux / Git Bash / WSL
```bash
bd export > .beads/issues.jsonl
bv --export-pages bv-site
```

#### Windows (PowerShell)
```powershell
# PowerShell requires special handling to avoid UTF-16 BOM corruption
$content = bd export | Out-String
[System.IO.File]::WriteAllText(".beads/issues.jsonl", $content.Trim(), [System.Text.UTF8Encoding]::new($false))
bv --export-pages bv-site
```

### Issue Closure Policy (CRITICAL)

**NEVER close issues (`bd close`) until a human has verified the work is functionally complete.**

- Tests passing is NOT sufficient for closure
- The user must confirm the feature works correctly in the actual app
- Keep issues `in_progress` until user gives explicit approval
- Only then run `bd close <id> --reason="User verified: <what they confirmed>"`

See `CLAUDE.md` → Multi-Agent Collaboration for full details.

## Gotchas

1. **macOS only**: Parakeet models use Apple Neural Engine; Whisper uses Metal GPU
2. **Path alias**: Use `@/` not `./src/` for imports (e.g., `@/components/ui/button`)
3. **NSPanel focus**: Pill window uses NSPanel to avoid focus stealing; test carefully
4. **Clipboard**: Text insertion preserves user clipboard; restored after 500ms
5. **Model preloading**: Models preload on startup; don't assume instant availability
6. **Tauri capabilities**: Permission changes require edits in `src-tauri/capabilities/`
7. **Large lib.rs**: Main Rust entry point at 96KB; navigate via module imports
8. **Sidecar builds**: Parakeet Swift sidecar built via `build.rs` during `tauri build`

## Key Files

- `src-tauri/src/lib.rs` — Main Rust entry, command registration
- `src-tauri/src/commands/` — All Tauri command implementations
- `src/hooks/` — React hooks for Tauri integration
- `src/components/tabs/` — Main UI tab components
- `src-tauri/capabilities/` — Tauri permission definitions

## References

- `agent-docs/ARCHITECTURE.md` — Detailed architecture diagrams
- `agent-docs/EVENT-FLOW-ANALYSIS.md` — Event system documentation
- `CLAUDE.md` — Coding assistant guidelines
- `README.md` — Product overview
