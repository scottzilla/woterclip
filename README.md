# WoterClip

Linear-backed agent orchestration for Claude Code. A single Claude instance wears different "hats" (personas) based on Linear issue labels — a CEO triages work, worker personas execute it.

## How It Works

```
Linear Issues → /heartbeat → Persona Matching → Work → Report Back
```

1. **Issues** live in Linear with persona labels (`backend`, `frontend`, etc.)
2. **Heartbeat** picks the highest-priority issue and resolves which persona handles it
3. **Personas** (CEO, Backend, Frontend, ...) define identity, tools, and runtime config
4. **Reports** are structured comments on the Linear issue with progress, commits, and blockers
5. **Schedule** runs heartbeats automatically via Claude Code's `/schedule`

The human is the **Board** — the ultimate escalation target when the agent is blocked.

## Install

```bash
claude plugin add /path/to/woterclip
```

## Quick Start

```bash
# 1. Initialize WoterClip in your repo (creates config + personas + Linear labels)
/woterclip-init

# 2. Run a single heartbeat cycle
/heartbeat

# 3. Or schedule recurring heartbeats
/schedule 30m /heartbeat

# 4. Check status
/woterclip-status
```

## Prerequisites

- Claude Code with Linear MCP connected
- Linear workspace with a target team

## Personas

Each persona gets its own directory with three files:

| File | Purpose |
|------|---------|
| `SOUL.md` | Identity, posture, voice, decision framework |
| `TOOLS.md` | Available tools and integrations |
| `config.yaml` | Runtime config (model, thinking effort, max turns) |

### Default Personas

| Persona | Role | Model | Label |
|---------|------|-------|-------|
| CEO | Triage, decompose, coordinate | Sonnet | *(default — no label)* |
| Backend | API, database, server-side | Opus | `backend` |
| Frontend | UI, components, styling | Sonnet | `frontend` |

Create custom personas with `/persona-create` or copy directories between repos.

## Commands

| Command | Description |
|---------|-------------|
| `/heartbeat` | Run one heartbeat cycle |
| `/heartbeat --dry-run` | Show what would be picked up |
| `/heartbeat --persona backend` | Force a specific persona |
| `/woterclip-init` | Initialize WoterClip in a repo |
| `/woterclip-status` | Current state, queue, blocked issues |
| `/woterclip-status --history` | Recent heartbeat history |
| `/persona-create` | Create a new persona interactively |
| `/persona-list` | List configured personas |

## Design

See [`docs/specs/2026-03-25-woterclip-design.md`](docs/specs/2026-03-25-woterclip-design.md) for the full design spec.

## License

MIT
