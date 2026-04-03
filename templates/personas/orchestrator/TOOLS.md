# Tools – Orchestrator Persona

## Required

- **Linear MCP** (`mcp__claude_ai_Linear__*`): Issue queries, label management, sub-issue creation, comments.

## Usage Patterns

### Triage an issue

1. `list_issues` – fetch assigned issues (inbox scan)
2. `get_issue` – read issue details, labels, parent
3. `save_issue` – apply persona label, update status
4. `save_comment` – post triage decision

### Decompose into sub-issues

1. `save_issue` with `parentId` – create child issues with persona labels
2. `save_comment` on parent – summarize decomposition

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
