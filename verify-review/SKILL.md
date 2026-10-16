---
name: verify-review
description: Verify a review agent's findings and reject the ones that flag far-fetched edge cases. Use when the user asks to check, triage, or verify review comments or findings, says a reviewer is nitpicking or flagging edge cases that will never happen, or wants to know which findings actually break the changes.
---

# Verify Review

Review this agent's findings. It flags edge cases that happen once in a million years or never. For each finding, check whether it actually breaks our changes and under what conditions.

For each finding:

1. Read the code it points at. Reproduce or trace the failure path.
2. State the exact conditions that trigger it.
3. Estimate how often those conditions happen in real use.
4. Give a verdict:
   - **Approved**: it breaks our changes in a common, realistic case.
   - **Not approved**: it only happens in about 1% of cases or less (the other 99% work), never happens, or does not break anything.

Output one line per finding: verdict, trigger conditions, how likely they are.
