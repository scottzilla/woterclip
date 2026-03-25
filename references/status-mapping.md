# Status Mapping

Maps between Linear issue states and WoterClip agent states.

## Linear States → WoterClip Behavior

| Linear State | WoterClip Behavior |
|-------------|-------------------|
| **Backlog** | Ignored — not in the inbox |
| **Todo** | In the inbox, eligible for pickup (lower priority than In Progress) |
| **In Progress** | In the inbox, priority pickup (agent or human started work) |
| **In Review** | Ignored — human is reviewing, agent should not touch |
| **Done** | Ignored — completed |
| **Canceled** | Ignored — canceled |

## WoterClip Outcomes → Linear State Changes

| Heartbeat Outcome | Linear State Transition | Labels Changed |
|-------------------|------------------------|----------------|
| **Work completed** | → Done (or In Review if PR opened) | Remove `agent-working` |
| **Work in progress** | Stay In Progress | Keep `agent-working` |
| **Blocked** | Stay In Progress | Remove `agent-working`, add `agent-blocked` |
| **Triaged by CEO** | Stay Todo | Add persona label |
| **Decomposed** | Parent stays In Progress, sub-issues created as Todo | Add persona labels to sub-issues |

## Inbox Query

The heartbeat fetches all issues assigned to "me" (resolved dynamically by Linear MCP), then filters client-side:

### Sort Order

1. **Status**: In Progress > Todo (all others filtered out)
2. **Priority**: Urgent > High > Medium > Low > No priority

### Filter Rules

- Skip issues without a persona label (unless CEO is default)
- Skip `agent-blocked` issues unless new human comments exist since last agent comment
- Skip issues in states other than Todo or In Progress
- If `--persona <name>` flag is set, only match that persona's label

## Stale Detection

- `agent-working` label with no heartbeat comment in the last `stale_lock_hours` → stale lock
- Heartbeat cleans stale locks: removes `agent-working`, posts a comment explaining the cleanup
