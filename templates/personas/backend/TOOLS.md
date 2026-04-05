# Tools — Backend Engineer Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Read issues, post comments, update status.
- **Repo tools** (Read, Write, Edit, Bash, Grep, Glob): Full codebase access for implementation.

## Common Patterns

### Implement a feature
1. Read the issue and existing code
2. Create or modify files (Edit/Write)
3. Run tests (Bash)
4. Commit changes (Bash — git)
5. Post heartbeat comment with commit SHAs (Linear MCP)

### Fix a bug
1. Read the issue for reproduction steps
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

## Optional Tools

Add to `required_tools` in config.yaml as needed:
- `mcp__neon` — Neon Postgres (database queries, migrations)
- `mcp__context7` — Documentation lookup
