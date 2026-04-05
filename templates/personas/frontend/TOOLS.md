# Tools — Frontend Engineer Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Read issues, post comments, update status, check attachments, and reference project documents.
- **Repo tools** (Read, Write, Edit, Bash, Grep, Glob): Full codebase access for implementation.

## Common Patterns

### Build a component
1. Read the issue and check for attachments (`get_attachment`) — mockups, wireframes, design references
2. Check existing components for patterns (Glob/Grep)
3. Create or modify component files (Write/Edit)
4. Run dev server and verify (Bash)
5. Commit and post heartbeat comment (Bash + Linear MCP)

### Reference project documents

1. `search_documentation` – find relevant project docs (UI guidelines, component patterns, design specs)
2. `get_document` – read a specific document before starting work
3. Check project docs for component conventions, design system rules, and accessibility requirements

### Fix a UI bug
1. Read the issue and check for attachments (`get_attachment`) — screenshots, reproduction steps, expected vs actual visuals
2. Find the relevant component (Grep/Glob)
3. Fix and test interactive states
4. Commit and comment

### Styling work
- Use the project's design system tokens (colors, spacing, typography)
- Check for dark mode compatibility if the project supports it
- Test responsive breakpoints

### Set issue relations

Use `save_issue` to link issues discovered during implementation:
- `blockedBy: ["ISSUE-456"]` – mark your issue as blocked by another
- `blocks: ["ISSUE-789"]` – flag that your issue blocks another
- `relatedTo: ["ISSUE-123"]` – link related issues
- `duplicateOf: "ISSUE-100"` – mark as duplicate (auto-cancels the issue)
- To remove: use `removeBlockedBy`, `removeBlocks`, or `removeRelatedTo` arrays

All relation arrays are **append-only** — existing relations are never removed unless you use the explicit remove fields.

## Memory

Use Claude Code's built-in memory for persistent context across sessions. Store memories relevant to component patterns, styling gotchas, and responsive edge cases.

- Before starting: check memory for past work on related components.
- After completing: save non-obvious findings — approaches that worked, visual pitfalls, accessibility issues.

## Optional Tools

Add to `required_tools` in config.yaml as needed:
- `mcp__plugin_playwright_playwright` — Browser automation for visual verification
- `mcp__context7` — Documentation lookup
