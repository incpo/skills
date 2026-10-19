---
name: performance-optimization
description: Measure-first playbook for making software faster or leaner, with real examples. Covers request hot paths, startup time, database queries, memory, bundle/image size, CI/build/test time, external API calls and caching. Use when asked to speed up, profile, slim down or cut the cost of code, queries, pipelines or infrastructure.
---

# Performance optimization

A measure-first workflow that applies to any part of a system: request hot paths,
startup time, database, memory, artifact size, CI/test pipelines, outbound calls and
background jobs. It is drawn from Daniel Lockyer's (@daniellockyer) optimization work
on Ghost, Ghost ActivityPub, Gumroad, knex-migrator, GScan and OpenTelemetry JS. The
`references/` files hold the real commits and PRs behind each technique.

## The playbook

1. **Name the metric and the scenario.** Pick one: p50/p95 latency of a page,
   requests/sec under `wrk`, boot time, peak or steady-state RSS, number of queries
   per request, bundle KB, image MB, CI wall-clock time. Use a realistic workload,
   such as "a site with a lot of posts", "large sellers" or "heavy theme". Many wins
   only show up at scale.
2. **Measure before touching code.** Use a CPU profile or flamegraph, a query log or
   counter, a heap snapshot, a bundle analyzer or CI step timings. If there is no way
   to measure, add one first (spans, timing logs, a benchmark job). Commits that only
   add instrumentation are good work on their own.
3. **Attack the widest bar first.** Fix whatever dominates the profile, even if it is
   unglamorous (a lodash helper, `moment()`, a duplicate call, a regex).
4. **Prefer the cheapest change that removes work**, roughly in this order:
   - don't do it (unused work, unused requests, unused dependencies)
   - do it only when needed (lazy, defer into the branch that uses it)
   - do it once (dedupe, hoist out of loops, memoize per request)
   - do it in bulk (batch, preload, `push_bulk`, `Promise.all`)
   - do it with a cheaper primitive (native over library, Set over regex, `EXISTS`
     over a subquery)
   - only then reach for caching or architecture changes.
5. **One optimization per commit, with the number in the message.** For example:
   "cuts ~5% CPU during boot of large sites", "79 queries → 1", "peak 480MB → 270MB".
   Keep behavior identical. Add or extract a unit test first when the code being
   rewritten is subtle.
6. **Fix it where it lives.** If the cost is inside a library you or your team
   maintain, fix it there and bump the version. If it's in an upstream OSS library,
   send the PR with benchmark numbers.
7. **Re-measure and keep or revert.** If a change doesn't move the metric, or it
   correlates with a regression (memory leak, flaky tests, heavier queries), revert
   it quickly. Say why in the revert message, and re-apply it once you've been proven
   wrong.
8. **Lock it in.** Add a benchmark or boot-time job to CI, a lint rule (for example,
   for migration performance), or a regression test so the win doesn't silently
   decay.

## Pick the direction

Read only the reference that matches the bottleneck. Several may apply.

| Symptom or goal | Read |
| --- | --- |
| No idea where time goes; need profiling or instrumentation | [references/measurement.md](references/measurement.md) |
| Slow request or CPU-bound function, high CPU under load, slow algorithm | [references/runtime-hot-paths.md](references/runtime-hot-paths.md) |
| Slow boot or cold start, heavy imports, unused frameworks loaded | [references/startup-and-loading.md](references/startup-and-loading.md) |
| Too many or slow queries, N+1, migrations, DB init, connection pool | [references/database.md](references/database.md) |
| High RSS, memory growth after uploads, leaks, fragmentation | [references/memory.md](references/memory.md) |
| Large JS bundle, heavy `node_modules`, large Docker image or package | [references/bundle-and-artifacts.md](references/bundle-and-artifacts.md) |
| Slow CI, builds, test suites, local dev loop | [references/ci-build-test.md](references/ci-build-test.md) |
| Slow third-party or API calls, caching, background jobs, HTTP clients | [references/network-caching-jobs.md](references/network-caching-jobs.md) |

## Commit / PR message shape

```
<Verb-first summary: Optimized / Lazyloaded / Removed / Deferred / Preloaded …>

ref <issue or profiling ticket>
- what the code does today and why that is slow (cite the profile/flamegraph %)
- what this change does instead
- the measured result (before → after, environment, workload)
- caveats / follow-ups ("ultimately we should …")
```

## Guardrails

- Behavior must stay identical. Lodash-to-native rewrites must preserve edge cases,
  such as `_.keys(undefined)` versus `Object.keys(undefined)` and `_.find` returning
  `undefined`.
- Test-only shortcuts must be gated to the test environment: fewer bcrypt rounds,
  disabled throttling, no background migrations. Never weaken production security.
- Forcing a DB index, changing pool sizes or adding caches changes production
  behavior. State the evidence and keep the change easy to revert.
- Don't make claims you didn't measure. If a gain is a hypothesis ("should help if
  we ever switch to Redis"), say so.

## Done when

The target metric has a before and after number on a realistic workload, behavior and
tests are unchanged, each optimization is its own reviewable change, and anything
unproven or reverted is called out.
