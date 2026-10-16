---
name: verify-review
description: Verify a review agent's findings and reject the ones that flag far-fetched edge cases. Use when the user asks to check, triage, or verify review comments or findings, says a reviewer is nitpicking or flagging edge cases that will never happen, or wants to know which findings actually break the changes.
---

# Verify Review

You are the requester. You filter the reviewer's findings yourself. The reviewer flags everything it can find, including rare edge cases. This is intentional.

Do not tell the reviewer agent to flag less, to skip edge cases, or how to filter its findings. Do not send it these criteria. If it knows about the filter, it finds less and misses real issues.

For each finding:

1. Read the code it points at. Reproduce or trace the failure path.
2. State the exact conditions that trigger it.
3. Estimate how often those conditions happen in real use.
4. Give a verdict:
   - **Approved**: it breaks our changes in a common, realistic case.
   - **Not approved**: it only happens in about 1% of cases or less (the other 99% work), never happens, or does not break anything.

Output one line per finding: verdict, trigger conditions, how likely they are.
