---
name: task
description: "Complete a specific Linear issue end-to-end, then manage its Linear status. ONLY use this skill when the user EXPLICITLY invokes it by mention — i.e. they type `/task ENG-305 ...`, or write an explicit imperative naming a Linear issue ID such as 'go complete ENG-305 ...', 'finish ENG-12 ...', 'do PROJ-441 ...'. The defining signal is an explicit Linear issue identifier (LETTERS-NUMBERS) the user is handing you to work on now. Do NOT trigger this skill for general questions about tasks, 'what am I working on', listing issues, or any request that does not explicitly hand you one issue ID to complete (that is the `mytasks` skill's job, not this one). This skill mutates real Linear issue statuses, so it must never fire speculatively."
---

# Complete a Linear issue (`/task`)

This skill does one job: take one Linear issue the user explicitly handed you, do the actual work to complete it, then keep its Linear status in sync — without ever fighting the user for control of that status.

It deliberately triggers **only on explicit mention**. The user invokes it on purpose (`/task ENG-305 ...` or "go complete ENG-305 ..."). It is not a helpful background reflex. The reason: it changes real issue statuses in a shared workspace, and a wrong auto-flip is annoying to undo and visible to the user's team. Explicit invocation is the safety mechanism — respect it.

## 1. Parse the invocation

Extract the issue identifier with the pattern `[A-Z]+-[0-9]+` (e.g. `ENG-305`, `APP-12`). If the message contains several IDs, treat each as its own run of this flow.

If no identifier is present, ask which issue — don't guess.

Judge what the user passed after the ID:
- **Just a short reminder / label** (e.g. "the login bug") → context only, you still need to fetch the issue in step 2.
- **The actual task content** — a real description, requirements, or acceptance criteria substantial enough to work from → treat it as authoritative and **skip the fetch in step 2**. The user is handing you the spec directly so you don't burn a round-trip re-fetching what they already typed.

## 2. Fetch the issue (skip if the content was passed in)

If step 1 decided the user already gave you the task content, **do not call `get_issue`** — use what they provided as the spec and go straight to step 3.

Otherwise, call Linear `get_issue` with `id` = the identifier. Read the title, description, and acceptance criteria so you actually understand what "done" means.

If the call fails:
- **Not found / wrong ID** → tell the user, stop. Don't invent work.
- **Auth / connection error** → tell the user the Linear plugin needs reconnecting, and wait for them to confirm before retrying. Don't loop.

## Linear context

Never hardcode project, team, or user. Resolve them when needed:

- **User**: Linear `get_user` with `query: "me"`.
- **Project**: match the current repo/workspace to a Linear project. Candidates: repo name (`basename "$(git rev-parse --show-toplevel)"`), workspace directory name, current branch. Call `list_projects` and pick the best loose match (ignore case and suffixes like `-ai`, `-app`). Example: workspace/repo `alladin-ai` → Linear project `Alladin`. If unclear, ask with `AskUserQuestion`.
- **Template**: whenever you create an issue (e.g. a follow-up or split-out task), use the template named after the authenticated user in most cases: `list_templates` with `type: "issue"`, pick `"<first name> template"` case-insensitively — e.g. user `Nick` → `nick template`. Pass it as `template` to `save_issue` and write the `description` in the template's structure (`get_template`), because a non-empty description replaces the template body. No such template → create without one.

If the fetched issue belongs to a different project than the workspace match, mention it to the user but still work on it — they handed you the ID on purpose.

## 3. Do the work

Implement the task for real, in the codebase, the way you would any normal engineering request — read the relevant code, make the change, run the checks that apply. The Linear bookkeeping in this skill wraps around real work; it does not replace it.

Only proceed to step 4 if the work is genuinely complete. If you're blocked, the task is ambiguous, or you only partially finished, **say so and leave the status untouched**. A truthful "I'm blocked on X" is worth far more than an issue parked in review that isn't actually ready.

## 4. Move to On Review

Once the work is really done, set the status:

```
save_issue  id=<IDENTIFIER>  state="On Review"
```

Then record that this issue is now skill-managed and in the review phase — see **State**. Briefly tell the user what you did and that it's moved to On Review.

If `save_issue` rejects the state name, the workspace uses different labels. Call Linear `list_issue_statuses` for the issue's team, pick the status whose type is `completed`-adjacent / clearly the review column, and use its exact name. Use the same fallback for "In Progress" (type `started`).

## 5. Follow-up changes — the revert-once rule

After an issue is On Review the user may, in plain conversation (no `/task` prefix), ask you to change something about it ("actually rename that function", "the copy should say X"). Behavior depends on the issue's recorded phase:

- **Phase `on_review`** (first change request since it went to review): set the issue back to `In Progress`, make the requested change, then record phase `hands_off`. Do **not** move it back to On Review afterward. The user takes the wheel on status from here — moving it back to On Review yourself is exactly the unwanted behavior this rule exists to prevent.
- **Phase `hands_off`**: make the requested change and **do not touch the status at all**, ever again, for that issue. The user controls it manually now.
- **No recorded phase**: this issue isn't managed by this skill — just do what's asked, no status changes.

A fresh explicit `/task <SAME-ID> ...` invocation is a deliberate new completion: run the full flow again from step 2 and reset the phase to `on_review` (re-arming the revert-once rule). The hands-off rule only governs implicit follow-up chatter, not an intentional re-invocation.

## State

Persist phase per issue in `~/.claude/.task-skill-state.json` so it survives across turns and context compaction. Shape:

```json
{ "ENG-305": "on_review", "APP-12": "hands_off" }
```

Read it before deciding follow-up behavior; rewrite the whole file after each phase change. Create it (`{}`) if absent. Read/Write the file directly — it's small.

## Why it's built this way

The user's underlying ask: "complete the thing, mark it for review, and once I start hand-correcting it, stop managing its status — I'll take it from here." The revert-once rule encodes that handover. Over-automating status is worse than under-automating it, because the user can always move a card themselves but can't easily un-notify a team that something was prematurely marked ready.
