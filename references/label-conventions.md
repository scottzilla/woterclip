# Label Conventions

WoterClip uses Linear labels for persona routing and agent state tracking.

## Label Group

All WoterClip labels live under a parent group (default: `WoterClip`). The group name is configurable in `config.yaml` → `labels.group`.

## State Labels

| Label | Purpose | Applied by | Removed by |
|-------|---------|------------|------------|
| `agent-working` | Agent is actively working this issue | Heartbeat (step 6) | Heartbeat (step 10) on done/blocked |
| `agent-blocked` | Agent is blocked, needs Board attention | Heartbeat (step 10) | Board (manually) or heartbeat when new context appears |

### State Label Rules

- `agent-working` and `agent-blocked` are mutually exclusive — never both present
- `agent-working` is added via read-modify-write: read current labels, append, save full set
- Stale `agent-working` labels (older than `stale_lock_hours`) are auto-cleaned by the heartbeat
- `agent-blocked` issues are skipped unless new human comments exist since the last agent comment

## Persona Labels

Persona labels route issues to the right persona. Created by `/woterclip-init`.

| Label | Persona | Typical signals |
|-------|---------|-----------------|
| `backend` | Backend Engineer | API, endpoint, route, database, migration, query, webhook |
| `frontend` | Frontend Engineer | Component, UI, page, layout, styling, responsive, animation |
| `infra` | Infra Engineer | Deploy, CI/CD, Docker, env vars, infrastructure |
| `qa` | QA Engineer | Test, coverage, E2E, integration test, flaky |
| *(none)* | CEO (default) | No code signals, architecture, cross-cutting, unlabeled |

### Persona Label Rules

- **One persona label per issue.** Never dual-label — decompose into sub-issues instead.
- Labels are applied by the CEO persona during triage, or manually by the Board.
- Custom persona labels are added via `/persona-create` and registered in `config.yaml`.

## Label Lifecycle

```
New issue (no labels)
  → CEO triages → adds persona label (e.g., "backend")
  → Heartbeat picks up → adds "agent-working"
  → Work completes → removes "agent-working"
  → Or blocked → removes "agent-working", adds "agent-blocked"
  → Board unblocks → removes "agent-blocked"
  → Next heartbeat picks up again
```

## Read-Modify-Write Pattern

Linear labels are managed as arrays. To add or remove a label:

1. `get_issue` — read current labels array
2. Modify the array (push or filter)
3. `save_issue` — save the full label set

This is safe because WoterClip runs as a single instance per repo — no concurrent writers.
