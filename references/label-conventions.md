# Label Conventions

WoterClip uses Linear labels exclusively for persona assignment — routing issues to the right agent persona.

**Labels = who owns the issue. States = where it is in the lifecycle.**

## Label Group

All WoterClip labels live under a parent group (default: `WoterClip`). The group name is configurable in `config.yaml` → `labels.group`.

## Persona Labels

Persona labels route issues to the right persona. Created by `/woterclip-init`.

| Label | Persona | Typical signals |
|-------|---------|-----------------|
| `backend` | Backend Engineer | API, endpoint, route, database, migration, query, webhook |
| `frontend` | Frontend Engineer | Component, UI, page, layout, styling, responsive, animation |
| `infra` | Infra Engineer | Deploy, CI/CD, Docker, env vars, infrastructure |
| `qa` | QA Engineer | Test, coverage, E2E, integration test, flaky |
| `ceo` | CEO | Strategy, prioritization, roadmap, architecture, cross-cutting |
| *(none)* | Orchestrator (default) | Unlabeled issues — routed by the Orchestrator |

### Persona Label Rules

- **One persona label per issue.** Never dual-label — decompose into sub-issues instead.
- Labels are applied by the Orchestrator during triage, or manually by the Board.
- Custom persona labels are added via `/persona-create` and registered in `config.yaml`.

## Label Lifecycle

```
New issue (no labels)
  → Orchestrator triages → adds persona label (e.g., "backend")
  → Heartbeat picks up → state moves to In Progress (no label change)
  → Work completes → state moves to Done/In Review (no label change)
  → Or blocked → state moves to Blocked (no label change)
  → Or reassigned → persona label swapped, state moves to Todo
```

## Reassignment

When an agent hands off to another persona:

1. Read current labels via `get_issue`
2. Remove own persona label, add target persona label
3. Save the full label set via `save_issue`
4. Move state to Todo (in the same `save_issue` call)
5. Post a handoff comment explaining what the next persona needs to do

## Read-Modify-Write Pattern

Linear labels are managed as arrays. To change a persona label:

1. `get_issue` — read current labels array
2. Modify the array (swap persona labels)
3. `save_issue` — save the full label set

This is safe because WoterClip runs as a single instance per repo — no concurrent writers.
