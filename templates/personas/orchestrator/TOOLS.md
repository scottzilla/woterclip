# Tools – Orchestrator Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Issue queries, label management, sub-issue creation, comments.

## Usage Patterns

### Triage an issue

1. `list_issues` – fetch assigned issues (inbox scan)
2. `get_issue` – read issue details, labels, parent, assignee
3. Skip issues that already have an assignee (claimed by another agent or human)
4. `save_issue` – apply persona label, update status
5. `save_comment` – post triage decision

### Claim and release issues

1. `save_issue` with `assignee: "me"` – claim an issue before dispatching to a persona worker
2. `get_issue` – check assignee before working; skip if already claimed
3. `save_issue` with `assignee: null` – release when the persona worker finishes (done or blocked)

### Decompose into sub-issues

1. `save_issue` with `parentId` – create child issues with persona labels
2. `save_comment` on parent – summarize decomposition

### Set issue relations

1. `save_issue` with `relatedTo: ["ISSUE-123"]` – link related issues during triage
2. `save_issue` with `blockedBy: ["ISSUE-456"]` – mark an issue as blocked by another
3. `save_issue` with `blocks: ["ISSUE-789"]` – mark an issue as blocking another
4. `save_issue` with `duplicateOf: "ISSUE-100"` – mark as duplicate (auto-cancels the issue)
5. To remove: use `removeRelatedTo`, `removeBlockedBy`, or `removeBlocks` arrays
6. To unmark duplicate: `duplicateOf: null`

All relation arrays are **append-only** — existing relations are never removed unless you use the explicit remove fields.

### Escalate to Board

1. `save_comment` – describe blocker, @-mention Board user
2. `save_issue` – move to Blocked state

## Sub-Agent Dispatch

- **Agent** tool: Spawn persona sub-agents for parallel issue processing.

### Spawning a persona sub-agent

1. Read the persona's `config.yaml` from `$AGENT_HOME` to extract `model` and `thinking_effort`.
2. Call `Agent` with:
   - `subagent_type`: `"persona-worker"`
   - `model`: from persona config.yaml `runtime.model`
   - `isolation`: `"worktree"` (always)
   - `prompt`: spawn prompt containing `$AGENT_HOME`, thinking effort, and issue context

### Spawn prompt template

    $AGENT_HOME = .woterclip/personas/{persona_name}
    Thinking effort: {thinking_effort}

    Issue: {issue_id} - {issue_title}
    Description:
    {issue_description}

    Recent comments:
    {formatted_comments}

### Parallel dispatch

To dispatch multiple sub-agents in parallel, include all Agent calls in a single message. Claude executes them concurrently.

### Collecting results

Each sub-agent returns a summary with: issue ID, final state, commits, sub-issues created, and escalation flag. The orchestrator does NOT update Linear — sub-agents handle their own state transitions.

## Not Used

The Orchestrator does not use repo tools (file read/write, git, bash, etc.) except `Read` for loading persona config.yaml files before dispatch.
