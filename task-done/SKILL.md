---
name: task-done
description: "Log already-completed work as a Linear issue in the Linear project matching the current workspace, assigned to the authenticated user and marked Done. Use ONLY when the user explicitly invokes it — they type `/task-done ...`, or write an explicit imperative like 'log this in linear', 'create a linear ticket for what I did', 'track this work in linear', 'we finished X, put it in linear as done'. The defining signal is the user handing you finished work to record after the fact. Do NOT trigger for planning new work, listing tasks, or completing an existing issue handed to you by ID ('finish ENG-305' → that's `task`). This skill creates/mutates real Linear issues, so it must never fire speculatively."
---

# Log completed work to Linear (`/task-done`)

This skill records work that is **already done** as a Linear issue in the project that matches the current workspace, assigned to the **authenticated Linear user**, and set to **Done**. The team tracks everything in Linear, so when work happened without a ticket, this back-fills one.

It triggers **only on explicit invocation** — it creates and closes real issues in a shared workspace, so a wrong auto-fire is visible to the team and annoying to undo. Explicit invocation is the safety mechanism; respect it.

## 1. Resolve the Linear context

Never hardcode project, team, or user IDs. Resolve them every run:

- **User**: Linear `get_user` with `query: "me"`. Keep its name — it picks the template below. Use `assignee: "me"` when saving.
- **Project**: match the current repo/workspace to a Linear project.
  - Candidates: repo name (`basename "$(git rev-parse --show-toplevel)"`), workspace directory name, and the current branch (`git rev-parse --abbrev-ref HEAD`).
  - Call Linear `list_projects` and pick the project whose name best matches a candidate. Match loosely: ignore case and common suffixes like `-ai`, `-app`, `-web`, `-api`.
  - Example: workspace/repo `alladin-ai` → Linear project `Alladin`.
  - Exactly one clear match → use it. Several or none → ask with `AskUserQuestion`, listing the closest projects as options.
- **Team**: take it from the chosen project (`get_project` → its teams). If the project has several teams, use the one whose key matches the branch's issue prefix, otherwise ask.
- **Template**: Linear `list_templates` with `team: <team>`, `type: "issue"`. Look for the template named after the user: `"<first name> template"`, matched case-insensitively — e.g. user `Nick` → `nick template`. Use it in most cases (see step 5). If it doesn't exist, create without a template.

## 2. Build the ticket content

Figure out what was actually done and turn it into a concise title + description.

Pull from, in this order:
1. **What the user passed in** the invocation — if they described the work, that's authoritative.
2. **Git / PR context** (run these; they're cheap):
   - `git log --oneline -20` and `git diff --stat` against the base branch (usually `main`/`master`) — what changed.
   - `gh pr view --json title,body,url,number 2>/dev/null` — if a PR exists, its title/body are the best summary; keep its URL for step 5.
3. **This session** — what you and the user just finished, if it isn't yet committed.

If none of these yield anything concrete, ask the user one line: "What did you finish? I'll log it." Don't invent work.

**Title**: imperative, capitalized first word, no trailing period — match existing issues in the project (e.g. "Fix clipped Y-axis labels on bar chart", "Add SEO title to logo API").

**Description** (Markdown, literal newlines — no escaped `\n`): keep it short.
- **Template found** → call `get_template` and follow its section structure, filling each section from the context above.
- **No template** → for a bug fix:
  ```
  ## Problem
  <what was wrong>

  ## Root cause
  <why, with `path/to/file` references>

  ## Fix
  <what changed>
  ```
  For a feature/chore: one-line summary + a short bullet list of key changes and files touched.

## 3. Check if it's already in Linear

Before creating anything, look for an existing issue for this work:

- **Branch-encoded id (strongest signal)**: if the current branch contains an issue identifier (e.g. `user/eng-123-some-slug` → `ENG-123`), confirm it with Linear `get_issue`.
- **Keyword search**: Linear `list_issues` with `project: <resolved project>`, `query: "<3-5 key words from the title>"`, `includeArchived: false`. A result whose title clearly describes the same work is a match.

Treat it as a match only when you're confident it's the same work. A vaguely-similar title is **not** a match — when unsure, lean toward "no match" and create new (the default path).

## 4. Decide — the core rule

- **No match found → just create it.** This is the default. Do NOT ask. Go to step 5 (create).
- **A match was found → ask first.** Use the `AskUserQuestion` tool — never auto-act on an existing issue:
  - Q: "Found `<ID>` — `<its title>`. This looks like the same work."
  - Options: `Mark <ID> as Done` (recommended) · `Create a new issue anyway`
  - On "Mark as Done" → step 5 (update path). On "Create new" → step 5 (create path).

## 5. Write to Linear

**Create path** (`save_issue`, no `id`):
```
save_issue
  title:       <from step 2>
  description: <from step 2>
  team:        <resolved team>
  project:     <resolved project>
  assignee:    "me"
  state:       "Done"
  template:    <user's template>                         # omit if none found
  links:       [{ url: <PR url>, title: <PR title> }]   # only if a PR/commit URL exists
```

A non-empty `description` replaces the template body, so the description must already follow the template's structure (step 2). The template still applies its labels and fields.

**Update path** (existing match the user chose to close):
```
save_issue  id="<ID>"  state="Done"
```

If `save_issue` rejects `state="Done"`, the workspace renamed the column: call `list_issue_statuses` for the team, pick the status whose type is `completed`, and use its exact name.

## 6. Report

Confirm in one line with the issue id and URL, e.g. "Logged `ENG-466` as Done → <url>". For the update path, say which issue you closed.

## Failure handling

- **Auth / connection error on any Linear call**: tell the user "Linear needs re-auth — reconnect it and say 'ready'", and wait. Don't loop.
- **Project/team/template "not found"**: re-run the matching in step 1 once; if it still fails, ask the user.

## Why it's built this way

The user's ask: "we track everything in Linear, but I sometimes do work without a ticket — back-fill one automatically and close it." Creating-without-asking is the default because that's the common case and friction defeats the purpose. The single guardrail is the duplicate check: if the work already has an issue, ask before touching it, so the skill never silently double-logs or closes the wrong card. Project and user are resolved from the workspace and the Linear session, so the skill works for anyone in any repo.
