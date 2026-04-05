# Comment Format

All heartbeat comments follow a structured template posted via `mcp__claude_ai_Linear__save_comment`.

## Standard Template

```markdown
**🤖 persona-name**

## Heartbeat #N — YYYY-MM-DD HH:MM UTC (duration)

**Status:** In Progress | Completed | Blocked

### What was done
- [`a1b2c3d`](link) feat(api): commit message
- Description of non-commit work

### Created sub-issues
- [WOT-XX](link) — Description (persona)

### What's next
- Next steps for this issue

### Blockers
None

---
*WoterClip · persona-name · [WOT-XX](link) · from [Heartbeat #N-1](link)*
```

## Blocked Template

```markdown
**🤖 persona-name**

## Heartbeat #N — YYYY-MM-DD HH:MM UTC (duration)

**Status:** Blocked

### Blocker
Clear description of what is blocking progress.

### Action needed
@Board-User-Name — specific ask for what they need to do.

### What was done before blocking
- Work completed before hitting the blocker

---
*WoterClip · persona-name · [WOT-XX](link)*
```

## Reassignment Template

```markdown
**🤖 original-persona-name**

## Heartbeat #N — YYYY-MM-DD HH:MM UTC (duration)

**Status:** Reassigned → persona-name

### What was done
- Work completed before handoff

### Handoff context
What the next persona needs to know and do.

### Why reassigning
Reason this work belongs to the other persona.

---
*WoterClip · original-persona-name · [WOT-XX](link) · from [Heartbeat #N-1](link)*
```

## Triage Template

```markdown
**🤖 orchestrator**

**Triage:** → persona-name

Routing rationale (one line, only for non-obvious routing).

---
*WoterClip · orchestrator · [WOT-XX](link)*
```

## Decomposition Template

```markdown
**🤖 orchestrator**

## Heartbeat #N — YYYY-MM-DD HH:MM UTC (duration)

**Status:** Decomposed

### Sub-issues created
- [WOT-AA](link) — Description (`backend`)
- [WOT-BB](link) — Description (`frontend`)

### Sequencing
WOT-AA blocks WOT-BB (backend API must exist before frontend can integrate).

---
*WoterClip · orchestrator · [WOT-XX](link)*
```

## Rules

- Always start the comment with `**🤖 persona-name**` so the persona is immediately visible (all agents comment as the same Linear user)
- Always include heartbeat counter (`#N`) and timestamp with duration
- Always include persona name and issue link in footer
- Reference previous heartbeat comment link for carry-forward context
- Blocked comments must name who needs to act (Board user's display name from config)
- Completion comments must list shipped commits/PRs with links
- Use `⚠️` prefix on status line for uncertain work: `**Status:** ⚠️ Completed (needs manual verification)`
- Fast-path triage comments: `**Triage:** → backend` for obvious routing
- Reassignment comments must explain what was done, what the next persona needs to do, and why the handoff is happening

## Heartbeat Counter

The counter is **derived from Linear comments**, not stored locally:

1. Parse the last WoterClip comment on the issue for `Heartbeat #N`
2. Increment N for the new comment
3. If no previous comment exists, start at `#1`
4. If comments are deleted, counter resets — this is informational, not functional

## Footer Format

The footer line connects the comment to its context:

- `WoterClip` — identifies this as an agent comment
- `persona-name` — which persona produced this work
- `[WOT-XX](link)` — link to the issue
- `from [Heartbeat #N-1](link)` — link to previous heartbeat comment (omit on first heartbeat)

> **Note:** The "from Heartbeat #N-1" link references the previous heartbeat comment. Use `list_comments` to find the previous WoterClip comment's ID. If the comment URL format is unavailable, omit the link and use plain text: `from Heartbeat #N-1`.
