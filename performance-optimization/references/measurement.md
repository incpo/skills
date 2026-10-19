# Measurement and instrumentation

Read this first when nobody knows where the time or memory goes. The pattern: make the
cost visible, find the widest bar, then fix it. A commit that only adds measurement is
legitimate.

## Tools by stack

- **Node.js CPU:** `node --cpu-prof` (open in Chrome DevTools or speedscope), `0x`,
  `clinic flame`. Load-test with `wrk` or `autocannon` while profiling.
- **Node.js boot:** time from process start to "ready", logged on every boot. Use
  `--cpu-prof` on boot. Time each `require` with `debug` or `performance.now()`.
- **Ruby/Rails:** `rack-mini-profiler` + `stackprof` (flamegraphs, even in production
  behind auth), `bullet` for N+1, query counts per request in logs.
- **Python:** `py-spy`, `cProfile` + snakeviz, `django-silk`.
- **Database:** slow query log, `EXPLAIN`/`EXPLAIN ANALYZE`, count queries per request
  or per boot.
- **Distributed:** OpenTelemetry spans (Sentry/Jaeger/Cloud Trace). A waterfall
  trace exposes sequential awaits.
- **Memory:** heap snapshots for the JS heap. Compare RSS against the heap: if RSS
  grows but the heap doesn't, suspect native allocations or fragmentation.
- **CI:** per-step durations. Run the same step locally to separate machine cost from
  real work.
- **Micro-benchmarks:** `benchmark.js`/`tinybench` (ops/sec ± %), and commit the
  benchmark next to the change.

## Real examples

- **Boot-time benchmark in CI with history.** It runs on pushes to `main` only and
  publishes results to a GitHub Pages chart so regressions are visible over time.
  [Ghost 05977d5](https://github.com/TryGhost/Ghost/commit/05977d5c26fb7468937f0a34a36d23a17691d22e)
- **Timing every model `require`.** Debug logging of model load times "has already
  found a couple of models which need optimizing".
  [Ghost 5a6b9e6](https://github.com/TryGhost/Ghost/commit/5a6b9e6d84683611f72826145f8f65a73da70e2c)
- **Metrics before fixing.** "We know the members importer is slow but we need metrics
  to prove it": import count, error count and runtime.
  [Ghost 077652e](https://github.com/TryGhost/Ghost/commit/077652e3f3635a2f70b32ad7ed0ac7650f085795)
- **Boot time in the log line.**
  [ActivityPub 674cac5](https://github.com/TryGhost/ActivityPub/commit/674cac5efa2fb1a3429d1bf9131dede0ae17aa77)
- **Testing a theory with a span.** He wrapped `buildActivity` in an OTel span to judge
  its impact on a slow Admin screen before changing anything.
  [ActivityPub a19e26f](https://github.com/TryGhost/ActivityPub/commit/a19e26f0f2b3e3968de3a801dd0a15b566954a27)
- **Dropping a tool that didn't help.** GCP Cloud Profiler was enabled, then reverted
  because "this doesn't really give much useful info for Node.js apps".
  [ActivityPub 8cdd266](https://github.com/TryGhost/ActivityPub/commit/8cdd266b2a4e0b83e79422588be04870b6bcd66e)
- **Production flamegraphs in Rails.** He enabled `stackprof` in all environments so
  `rack-mini-profiler` can render flamegraphs in production, then upgraded to
  mini-profiler v4. [Gumroad #276](https://github.com/antiwork/gumroad/pull/276),
  [#323](https://github.com/antiwork/gumroad/pull/323)
- **Flamegraph and benchmark in an upstream PR.** OpenTelemetry JS `setAttributes`
  showed overhead compared with `setAttribute` in the flamegraph. The PR includes
  before and after `ops/sec`.
  [opentelemetry-js #6514](https://github.com/open-telemetry/opentelemetry-js/pull/6514)
- **Correcting misleading metrics.** Mailgun timing included time spent in a slow
  batch handler, so the slow DB looked like slow Mailgun. He wrapped only the SDK call.
  (Ghost 9bb2bc8, "Fixed erroneous timing in reported Mailgun metrics")
- **Name functions so flamegraphs are readable.** He renamed anonymous deserializers
  ([Ghost df76883](https://github.com/TryGhost/Ghost/commit/df76883378)) and sent
  [express-lazy-router #8](https://github.com/azu/express-lazy-router/pull/8)
  ("Added name to middleware function").

## Checklist

- [ ] One metric, one realistic workload, written down before changing code
- [ ] Profile captured; top 3 frames or queries identified with % share
- [ ] If there's no way to measure, add instrumentation as a separate change
- [ ] Re-run the exact same measurement after the change; record both numbers
