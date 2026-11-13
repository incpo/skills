---
name: pre-pr
description: Run the pre-PR pass on the workspace changes. Unslop the prose, start a GPT review session in Conductor with Conductor's review prompt, verify its findings with verify-review, fix the approved ones, and resolve the rejected ones. Use for /pre-pr, "pre-pr", "review before PR", or before opening a PR.
---

# Pre-PR

Do the steps in order. Do not ask the user between steps.

## 1. Unslop

Apply the unslop skill to the prose this branch adds or changes. Prose means Markdown files, code comments, docstrings, OpenAPI and tool descriptions, and error messages. The branch's changes are its commits against the target branch, its uncommitted changes, and its untracked files. Edit only lines the branch touched. Do not change code behavior.

## 2. Cross-review

Start a Codex session in this Conductor workspace. Send it Conductor's review prompt, `review-prompt.md` in this skill's directory, without changes:

```bash
sid=$(uuidgen | tr A-Z a-z)
conductor session create --agent codex --model gpt-6-astra --effort high \
  --session-id "$sid" --name "Pre-PR review" \
  --message-file <skill-dir>/review-prompt.md
echo "$sid"
```

Wait for it in the background. A review takes 5 to 30 minutes.

```bash
sleep 30; until [ "$(conductor session status <sid> --json | jq -r .status)" = idle ]; do sleep 20; done
```

The reviewer posts each finding as a diff comment. Read them with `mcp__conductor__GetDiffComments`.

Do not edit `review-prompt.md` or message the reviewer to make it flag less. You filter the findings in step 3, and the reviewer must not know about it.

## 3. Verify

Apply the verify-review skill to every finding from step 2. Output one line per finding with its verdict, the conditions that trigger it, and how likely those conditions are.

## 4. Resolve rejected findings

Resolve the diff comment of each rejected finding in Conductor, so the Changes panel shows only the findings you fix. If no available tool resolves diff comments, list the rejected comments in the report with their file and line, and tell the user to resolve them in the Changes panel.

## 5. Fix

Fix the approved findings at the root cause. Run the project's tests and type checks that cover the touched code.

For each approved finding, check whether a lint rule, a type, or a test would have caught it. If a test is cheap, add it with the fix. If a lint rule or type change is the better fix but is out of scope, list it in the report instead.

## Report

- The findings with their verdicts, and what you fixed.
- The rejected comments you resolved, or the ones the user must resolve.
- The lint, type, or test proposals from step 5.
- The review session id, so the user can open it in Conductor.
