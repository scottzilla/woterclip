# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

WoterClip is a **Claude Code plugin** (no runtime code — entirely markdown/YAML). It provides Linear-backed agent orchestration with persona-based task routing. A single Claude instance wears different "hats" (personas) based on Linear issue labels.

**Design spec:** `docs/specs/2026-03-25-woterclip-design.md`
**Implementation plan:** `docs/specs/2026-03-25-woterclip-implementation-plan.md`
**Linear:** WotAI workspace, WoterClip project

## Architecture

### Two-level structure

1. **Plugin** (this repo) — ships commands, skills, agents, references, and persona templates. Installed via `claude plugin add`.
2. **Per-repo scaffold** (`.woterclip/`) — created by `/woterclip-init` in target repos. Contains `config.yaml`, persona directories, heartbeat log, and lockfile.

### Core loop

```
/heartbeat → Load Config → Check Inbox (Linear) → Pick Issue → Resolve Persona
  → Validate Tools → Lock Issue → Understand Context → Do Work → Report → Update State → Next/Exit
```

The heartbeat is a **skill** (`skills/heartbeat/SKILL.md`), not code. Claude follows it as a procedure using Linear MCP tools and repo tools.

### Persona system

Each persona = directory with 3 files:
- `SOUL.md` — identity injected into Claude's context (shapes behavior)
- `TOOLS.md` — available tools and usage patterns (shapes capabilities)
- `config.yaml` — machine-readable runtime config (model, thinking effort, max turns, required tools)

Routing: Linear issue label → `personas` map in config.yaml → persona directory.

### Persona hierarchy

- **Board** (human) – ultimate escalation target
- **CEO** persona – strategic decisions, prioritization, architecture (label: `ceo`)
- **Orchestrator** persona – mechanical triage/routing, default for unlabeled issues (label: none, `is_default: true`)
- **Worker personas** (Backend, Frontend, etc.) – implementation, escalate to CEO

### Key conventions

- **Labels = who, States = what.** Persona labels (`backend`, `frontend`, `ceo`) indicate which agent owns an issue. Linear workflow states (Todo → In Progress → Blocked → In Review → Done) track lifecycle. No status labels — states are the single source of truth. Labels are managed via read-modify-write (get labels array → modify → save full set).
- **Heartbeat counter is derived from comments**, not stored locally. Parse last `Heartbeat #N` from Linear comments.
- **Lockfile** (`.woterclip/.heartbeat-lock`) prevents concurrent heartbeats. Must be deleted on every exit path.
- **`${CLAUDE_PLUGIN_ROOT}`** — use this for all intra-plugin path references in commands and hooks. Never hardcode paths.
- **Templates use `{{USER_NAME}}` and `{{TEAM}}`** placeholders — the init skill replaces these when scaffolding.

## Plugin Component Map

| Type | Location | Auto-discovery |
|------|----------|---------------|
| Manifest | `.claude-plugin/plugin.json` | Required |
| Commands | `commands/*.md` | By filename |
| Skills | `skills/*/SKILL.md` | By SKILL.md presence |
| Agents | `agents/*.md` | By filename |
| Hooks | `hooks/hooks.json` | By convention |
| References | `references/*.md` | Referenced by skills |
| Templates | `templates/` | Used by init skill only |

## Working on This Repo

This repo has no build system, no tests, no dependencies. "Development" means editing markdown and YAML files.

**To test the plugin locally:** `claude --plugin-dir /Users/alexkim/Documents/Github-Mac-2026/woterclip`

**Validation checklist:**
- YAML files parse cleanly (`python3 -c "import yaml; yaml.safe_load(open('file.yaml'))"`)
- SKILL.md files have valid frontmatter (`name` and `description` fields)
- Command .md files have valid frontmatter (`description` field)
- Agent .md files have valid frontmatter (`description` field)
- All file references in skills resolve (e.g., `${CLAUDE_PLUGIN_ROOT}/references/comment-format.md`)

## Editing Guidelines

- **Skills must use imperative/infinitive form** — "Read the config" not "You should read the config"
- **Skill descriptions must use third person** — "This skill should be used when..." not "Use this skill when..."
- **SKILL.md body target: 1,500-2,000 words.** Move detailed content to `references/` files.
- **Persona SOUL.md files are instructions TO Claude** — write them as identity directives, not documentation.
- **Config schema changes require bumping `version` field** in `templates/config.yaml` and updating the init skill's migration logic.
- **One persona label per issue.** The entire system assumes this — never design for dual-labeling.
