---
name: remove-overcode
description: Find and remove needless code, indirection, abstraction, defensive ceremony, and speculative flexibility while preserving behavior and meaningful boundaries. Use whenever a user asks to simplify, clean up, reduce abstractions, remove overengineering or boilerplate, review “overcode,” make code more direct, apply YAGNI, or questions why a wrapper, factory, helper, interface, injection point, or query layer exists. Trigger for both review-only requests and implementation requests, even when the user does not name “overcode.”
---

# Remove Overcode

Make the code express the current requirements with the fewest meaningful concepts. Remove machinery that only predicts hypothetical needs. Preserve machinery that enforces a real rule or boundary.



## Start with scope

Honor the files and directories the user placed in scope. Read local instructions before judging the code. Do not broaden a focused cleanup into an architectural rewrite

Determine whether the user asked for:

- a review: report findings without editing;
- a cleanup: edit the code and verify it;
- an explanation: explain why a construct is or is not justified.

Use the existing diff as context. Preserve unrelated work.

## Establish evidence

Before calling something overcode, answer:

1. What concrete job does it perform today?
2. How many independent consumers use it?
3. Does a runtime, framework, transaction, security, or test boundary require it?
4. Would inlining it hide a domain rule or repeat complex logic?
5. Can it be removed without changing observable behavior?

Search permitted scope for consumers when local instructions allow it. If scope prevents confirming usage, label the conclusion as based on the visible files.

Do not assume every abstraction is bad. Abstraction earns its place through current reuse, a meaningful name, enforced invariants, or a real boundary.

## Strong overcode signals

Prioritize these patterns.

### Speculative flexibility

- Dependency injection with one fixed implementation and no demonstrated alternate consumer.
- Factories that accept no configuration and create one singleton.
- Strategy, adapter, provider, or repository layers that only forward calls.
- Options, generics, callbacks, or extension hooks unused by current requirements.

Remove the seam when it has no current purpose. Keep it when tests, transactions, multiple runtimes, or real implementations depend on it.

### Trivial indirection

- A helper whose body is one obvious expression or method call.
- Pass-through functions that rename nothing and enforce nothing.
- A type alias or interface that merely repeats an implementation and has no independent contract value.
- A separate file used by one consumer solely to shorten that consumer.
- Extra comments that are unnecessary or inconsistent with local style.
- Defensive checks or try/catch blocks that are abnormal for trusted code paths.
- Casts to `any` used only to bypass type issues; fix the underlying types instead.
- Deep nesting that can be simplified with early returns.
- Other unnecessary patterns inconsistent with the file and surrounding codebase.

Inline straightforward operations. Keep a small helper when its name defines a non-obvious domain rule, it validates an invariant, or a framework boundary requires it.

### Defensive noise

- Non-null assertions after a guard that already narrows the value.
- Repeated casts compensating for a type that can be corrected once.
- Redundant defaults, branches, or null checks made impossible by prior validation.
- Validation repeated at multiple layers without different trust boundaries.

Remove only defenses proven redundant. Database, network, user-input, and deserialization boundaries still need runtime validation.

### Data-access ceremony

- Selecting a constant from the database only to return the same constant.
- Wrapping a database call in a local executor that only calls the database.
- Mapping a result through identity transformations.
- Fetching fields that are discarded or overwritten immediately.
- Building a generic query layer for one concrete query.

Keep parameterization, transaction control, authorization, tracing, retry behavior, and result validation when they perform real work.

### Premature sharing

- A shared utility used by one module.
- Extraction justified only by file length.
- Helpers grouped by technical shape despite belonging to one consumer’s domain flow.

Keep a function near its sole consumer. Extract it when multiple modules reuse it or a runtime/build boundary requires separation.

## Review workflow

1. Inspect the requested scope and its local instructions.
2. List candidate patterns with exact file references.
3. Trace each candidate’s callers or consumers when allowed.
4. Classify each candidate:
   - high confidence: removable without behavior change;
   - medium confidence: simpler design exists, but contract or external usage needs confirmation;
   - keep: abstraction enforces a present rule or boundary.
5. Rank findings by value, not by line count.
6. Recommend the smallest coherent cleanup.

Do not pad the report with harmless preferences. If the code is already appropriately direct, say so.

## Cleanup workflow

When the user authorizes changes:

1. Remove the abstraction and update its consumers in one coherent edit.
2. Delete types, imports, parameters, and helpers made unused by the change.
3. Preserve public behavior, error semantics, and data shapes unless fixing a clear bug or the user approves a contract change.
4. Keep functions in the same file as their sole consumer unless a real boundary requires extraction.
5. Follow the project’s import style, formatting, and file-order rules.
6. Inspect the diff for accidental formatting churn.
7. Run proportionate checks: focused tests or type checking when available, plus syntax/build and diff checks.

If removing an exported symbol could break unseen consumers, search for them when permitted. Otherwise state the uncertainty before making a breaking change.

## Avoid cleanup theater

Do not replace one abstraction with another. Common failure modes include:

- replacing a tiny factory with a builder;
- consolidating two clear domain functions into a configurable generic helper;
- introducing a utility solely to remove repeated one-line operations;
- changing names and formatting without reducing concepts;
- deleting validation because it looks verbose;
- turning readable code into dense expressions to minimize lines.

The goal is fewer concepts, not merely fewer lines.


## Guardrails

- Keep behavior unchanged unless fixing a clear bug.
- Prefer minimal, focused edits over broad rewrites.
- Preserve checks and error handling that enforce real trust boundaries or contracts.
- Keep the final summary concise (1–3 sentences).

## Report format

For a review, use:

```markdown
1. Finding — confidence
   file:line
   Why it is overcode and the smallest safe simplification.
```

End with a short “Keep” section for abstractions that may look excessive but enforce real rules.

For an implemented cleanup, summarize in 1–3 sentences:

- what was removed;
- what behavior was preserved;
- validation performed;
- any remaining uncertainty.

## Quick examples

Remove:

```ts
const createService = (
  execute = async (query: SQL) => database.execute(query)
) => ({
  load: async () => execute(query),
});

export const service = createService();
```

when no alternate executor or factory consumer exists. Export the service directly and call `database.execute`.

Keep:

```ts
function validateAccountId(value: string): AccountId {
  if (!ACCOUNT_ID_PATTERN.test(value)) throw invalidAccountId();
  return value as AccountId;
}
```

even with one consumer, because it names and enforces a domain invariant.

Keep dependency injection when production, tests, transactions, or different runtimes supply meaningful implementations. The presence of a default alone does not prove overcode.
