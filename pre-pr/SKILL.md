---
name: pre-pr
description: Run the pre-PR pass on the workspace changes. Unslop the prose, start a GPT review session in Conductor with Conductor's review prompt, verify its findings with verify-review, and fix the approved ones. Use for /pre-pr, "pre-pr", "review before PR", or before opening a PR.
---

# Pre-PR

Do the steps in order. Do not ask the user between steps.

## 1. Unslop

Apply the unslop skill to the prose this branch adds or changes: Markdown files, code comments, docstrings, OpenAPI/tool descriptions, and error messages in the diff (committed changes against the target branch, uncommitted changes, and untracked files). Edit only lines the branch touched. Do not change code behavior.

## 2. Cross-review

Start a Codex session in this Conductor workspace with Conductor's review prompt (`review-prompt.md` in this skill's directory, sent verbatim):

```bash
sid=$(uuidgen | tr A-Z a-z)
conductor session create --agent codex --model gpt-6-astra --effort high \
  --session-id "$sid" --name "Pre-PR review" \
  --message-file <skill-dir>/review-prompt.md
echo "$sid"
```

Wait for it in the background (5 to 30 minutes):

```bash
sleep 30; until [ "$(conductor session status <sid> --json | jq -r .status)" = idle ]; do sleep 20; done
```

The reviewer posts each finding as a diff comment. Read them with `mcp__conductor__GetDiffComments`.

Do not edit `review-prompt.md` or message the reviewer to make it flag less. Filtering happens in step 3, and the reviewer must not know about it.

## 3. Verify

Apply the verify-review skill to every finding from step 2. Output one line per finding: verdict, trigger conditions, how likely they are.

## 4. Fix

Fix the approved findings at the root cause. Run the project's tests and type checks that cover the touched code.

For each approved finding, check whether a lint rule, a type, or a test would have caught it. If a test is cheap, add it with the fix. If a lint rule or type change is the better fix but is out of scope, list it in the report instead.

## Report

- Findings with verdicts, and what you fixed.
- Lint, type, or test proposals from step 4.
- The review session id, so the user can open it in Conductor.
