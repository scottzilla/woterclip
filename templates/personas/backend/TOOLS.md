# Tools — Backend Engineer Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Read issues, post comments, update status, check attachments, and reference project documents.
- **Repo tools** (Read, Write, Edit, Bash, Grep, Glob): Full codebase access for implementation.

## Common Patterns

### Implement a feature
1. Read the issue and check for attachments (`get_attachment`) — specs, API docs, data samples
2. Read existing code
3. Create or modify files (Edit/Write)
4. Run tests (Bash)
5. Commit changes (Bash — git)
6. Post heartbeat comment with commit SHAs (Linear MCP)

### Reference project documents

1. `search_documentation` – find relevant project docs (API specs, architecture decisions, conventions)
2. `get_document` – read a specific document before starting work
3. Check project docs for coding conventions, API contracts, and integration notes

### Fix a bug
1. Read the issue and check for attachments (`get_attachment`) — error screenshots, log samples, reproduction data
2. Find relevant code (Grep/Glob)
3. Fix and add regression test
4. Commit and comment

### Database work
- Use Neon MCP (`mcp__neon__*`) if available for database operations
- Always create reversible migrations
- Test migration up and down paths

### Set issue relations

Use `save_issue` to link issues discovered during implementation:
- `blockedBy: ["ISSUE-456"]` – mark your issue as blocked by another
- `blocks: ["ISSUE-789"]` – flag that your issue blocks another
- `relatedTo: ["ISSUE-123"]` – link related issues
- `duplicateOf: "ISSUE-100"` – mark as duplicate (auto-cancels the issue)
- To remove: use `removeBlockedBy`, `removeBlocks`, or `removeRelatedTo` arrays

All relation arrays are **append-only** — existing relations are never removed unless you use the explicit remove fields.

## Memory

Use Claude Code's built-in memory for persistent context across sessions. Store memories relevant to implementation patterns, integration gotchas, and API quirks.

- Before starting: check memory for past work on related code areas.
- After completing: save non-obvious findings — what worked, what failed, integration surprises.

## Optional Tools

Add to `required_tools` in config.yaml as needed:
- `mcp__neon` — Neon Postgres (database queries, migrations)
- `mcp__context7` — Documentation lookup
