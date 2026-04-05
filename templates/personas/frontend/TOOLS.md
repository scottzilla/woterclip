# Tools — Frontend Engineer Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Read issues, post comments, update status.
- **Repo tools** (Read, Write, Edit, Bash, Grep, Glob): Full codebase access for implementation.

## Common Patterns

### Build a component
1. Read the issue for requirements and linked designs
2. Check existing components for patterns (Glob/Grep)
3. Create or modify component files (Write/Edit)
4. Run dev server and verify (Bash)
5. Commit and post heartbeat comment (Bash + Linear MCP)

### Fix a UI bug
1. Read the issue for reproduction steps
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

## Optional Tools

Add to `required_tools` in config.yaml as needed:
- `mcp__plugin_playwright_playwright` — Browser automation for visual verification
- `mcp__context7` — Documentation lookup
