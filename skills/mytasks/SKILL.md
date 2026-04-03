---
name: mytasks
description: "Show the user's assigned Linear tasks for a specific project. Use whenever the user asks about their tasks, issues, todos, or work items — e.g. 'my tasks', 'what am I working on', 'show my issues', '/mytasks ProjectName'. Trigger even without the word 'Linear' if they're asking about tasks in a project context."
---

# My Linear Tasks

## Get tasks

If the user specified a project (e.g. `/mytasks Mobile App`), call `mcp__claude_ai_Linear__list_issues` directly with `assignee: "me"`, `project: <name>`, `includeArchived: false`.

Otherwise call `mcp__claude_ai_Linear__list_projects`, then use the `AskUserQuestion` tool to let the user pick interactively. Use the project names as option labels and summaries as descriptions. If there are more than 4 projects, show the first 4 — the user can type a custom name via "Other".

If any MCP call fails with an auth error:
1. Run `open "https://claude.ai/settings/integrations"` to open browser
2. Say: "Linear needs re-auth — I opened the integrations page. Reconnect Linear and say 'ready'."
3. Wait for confirmation before retrying.

## Display

Skip "Done" tasks — only show active statuses.

Group by status in this order: 1) Todo, 2) In Progress, 3) On Review. Sort by priority within each group. Priority markers: `!!!` Urgent, `!!` High, `!` Normal, `-` Low.

```
## Project Alpha — My Tasks (6)

### Todo (2)
| Pri | ID      | Title                       |
|-----|---------|-----------------------------|
| !!  | ALF-100 | Write API docs              |
| -   | ALF-110 | Update README               |

### In Progress (3)
| Pri | ID      | Title                       |
|-----|---------|-----------------------------|
| !!  | ALF-123 | Fix auth token refresh      |
| !   | ALF-145 | Add pagination to user list |

### On Review (1)
...
```

No tasks → "No tasks assigned to you in [project]."
