---
name: simplify-sql
description: Refactor overcomplicated or AI-generated SQL into clear, maintainable, efficient queries while preserving results. Use when SQL contains excessive CTEs, nested subqueries, fake aggregation, unnecessary casts or null checks, accidental deduplication, or joins that are difficult to reason about. Also use for SQL simplification reviews and before-and-after PostgreSQL plan comparisons.
---

# Simplify SQL

Produce the smallest query that clearly expresses the required result and business rules. Preserve semantics before optimizing style.

## Establish the contract

Start at the final projection and identify:

- the columns and aliases the caller consumes;
- the required row grain, such as one row per user, order, or event;
- ordering, pagination, null behavior, and tie-breaking rules;
- filters and aggregations that represent business logic.

Use the schema, constraints, indexes, and calling code when available. If the intended row grain or duplicate-selection rule cannot be inferred safely, ask for that specific information rather than guessing.

## Trace the data model

Identify the anchor table and the shortest valid join path to each output field. For every join, state or verify its cardinality: one-to-one, one-to-many, or many-to-many. Treat unexpected row multiplication as a join or data-model question, not automatically as a deduplication problem.

Trust enforced `NOT NULL`, unique, primary-key, and foreign-key constraints. Do not assume undocumented constraints or confuse TypeScript types with database guarantees.

## Challenge complexity

Inspect each construct and retain it only when it carries semantics, improves readability, or produces a better measured plan.

- `MAX()` around non-aggregated fields plus a broad `GROUP BY` often hides row multiplication. Fix the join or encode the actual row-selection rule.
- `DISTINCT` and `DISTINCT ON` are not generic join fixes. In PostgreSQL, use `DISTINCT ON` only with an `ORDER BY` that makes the chosen row deterministic and matches the business rule.
- Window functions such as `ROW_NUMBER()` are appropriate when ranked row selection is intentional. Remove them when they merely conceal an incorrect join.
- Flatten pass-through CTEs and nested subqueries when doing so clarifies the query. Do not assume every CTE is an optimization fence: modern PostgreSQL can inline side-effect-free, non-recursive CTEs. Retain CTEs that express a meaningful stage, are reused, require materialization, or improve the plan.
- Remove redundant casts, null checks, empty-string checks, and defensive predicates only when schema constraints or upstream normalization prove them unnecessary.
- Keep aggregation, `EXISTS`, lateral joins, subqueries, or pre-aggregation when they correctly protect the intended row grain.

## Rebuild from the result grain

Prefer rewriting the clean query from the anchor table instead of mechanically editing the original:

1. Form the minimal `FROM` and direct joins.
2. Add only predicates required by the business rule.
3. Add explicit aggregation or deterministic row selection only where the result grain requires it.
4. Project only the contract columns.

Use clear domain aliases and standard SQL constructs. Avoid introducing wrappers or helpers that merely relocate complexity.

## Verify before claiming equivalence

When a runnable database or representative fixture is available:

- compare both result sets, including duplicate multiplicity, nulls, and ordering when ordering is contractual;
- test edge cases such as missing related rows, multiple related rows, ties, and empty input;
- compare `EXPLAIN (ANALYZE, BUFFERS)` plans under comparable conditions and report actual evidence rather than promising that a flatter query is faster.

For unordered read queries, an `EXCEPT ALL` comparison in both directions is a useful equivalence check. Compare ordered output separately when order matters. Remember that `EXPLAIN ANALYZE` executes the statement; do not use it on mutating SQL without an explicitly safe transaction or disposable environment.

If execution is unavailable, provide the rewritten query with clearly labeled assumptions and a concise verification plan. For review-only requests, explain proposed changes without editing files or running mutating statements.

## Deliverable

Lead with the simplified SQL. Then summarize the semantic changes, assumptions, and verification evidence. Call out any suspicious construct that could not be removed because its business rule is unknown.
