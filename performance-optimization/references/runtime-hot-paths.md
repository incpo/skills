# Runtime hot paths and algorithms

Use this for CPU-bound requests, functions called thousands of times per request or
boot, and work that scales badly with data size.

## Techniques, cheapest first

1. **Skip work whose result is unused.** Compute fallbacks, descriptions, headers or
   serializations only in the branch that needs them. Move the expensive expression
   inside the `if`.
2. **Do it once.** Hoist loop-invariant values out of loops. Reuse a `toJSON()` or
   sanitize result instead of recomputing it. Never call the same context or lookup
   function twice per request.
3. **Fast path for the common case.** If 99% of inputs are a simple type, return early
   before the general (slow) path.
4. **Replace heavy library helpers on hot paths with native code:**
   - lodash `_.find`/`_.each`/`_.pick`/`_.union`/`_.keys`/`concat` → native `find`,
     `for` loops, spread, `Object.keys`
   - `Object.assign` in constructors → direct assignment
   - `Object.entries` loops → `for...in`; track counts in a variable instead of
     `Object.keys(x).length` in a loop
   - `moment()` → `Date` or a tiny formatter; don't wrap a moment in another moment
   - `cloneDeep` → avoid entirely, or clone only the fields you need
5. **Fix the algorithm.** Replace O(n²) array membership checks with keys, a `Set` or
   a `Map`. Precompute normalized values once instead of per comparison. Replace
   recursion plus `concat`/clone with a single `reduce`. Rewrite regexes with nested
   quantifiers (catastrophic backtracking), or swap regex scans for `Set` lookups.
6. **Parallelize independent I/O.** Run independent awaits through `Promise.all`; a
   waterfall trace is the hint.

Preserve semantics. Native replacements can differ on `undefined`/`null` input. Add
fallbacks (`Object.keys(x || {})`) and keep tests green. Extract the function and
unit-test it before optimizing when the logic is subtle.

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| Settings cache: return early when the cached value is already a string, instead of `JSON.parse` on every lookup (hundreds per request) | **+30% requests/sec** under `wrk`; it had been up to 20% of CPU | [Ghost ea6d3a0](https://github.com/TryGhost/Ghost/commit/ea6d3a0f26b7f48e745f1c43f8d84e3e47b03970) |
| `_.find` → native `find` in URL service lookup during post import | lodash had been ~92% of CPU; imports "dramatically" faster | [Ghost da3142e](https://github.com/TryGhost/Ghost/commit/da3142ec00b064f68cb0e1baafe1065b9cf3f6fe) |
| `Object.assign` removed from the `Resources` constructor | −15% boot CPU on large sites | [Ghost b376585](https://github.com/TryGhost/Ghost/commit/b376585c640bf999bb5494b73f83b84292b11491) |
| `lodash.pick/union` → native | −5% boot CPU on large sites | [Ghost dd68fca](https://github.com/TryGhost/Ghost/commit/dd68fca9686cb83aa4e048a1f55586c57852cbba) |
| moment removed from the sitemap generator | −5% boot CPU | [Ghost 30220aa](https://github.com/TryGhost/Ghost/commit/30220aa6effd043c0b120a975e6e484adb86c118) |
| Deferred `_generateUrl` until after the filter check | −4% boot CPU | [Ghost 90fbbe1](https://github.com/TryGhost/Ghost/commit/90fbbe135f1a54a340e79645848f128995fb777c) |
| Moved a heavy `permittedAttributes` call into the `if` that uses it | −2% CPU | [Ghost 7bd70a3](https://github.com/TryGhost/Ghost/commit/7bd70a3ab22669ab07a06ee6b582d4865e91fdb0) |
| `concat` → spread in `sanitize.permittedOptions` | −1.5% boot | [Ghost b3cf23e](https://github.com/TryGhost/Ghost/commit/b3cf23ebd8f555b4b2da1cea3027097323a4e8a4) |
| Removed `moment(moment(x))` double wrap | −1% boot | [Ghost b49210f](https://github.com/TryGhost/Ghost/commit/b49210f4dd4f89f0e4321e8a83306644e6bab56d) |
| Removed lodash from Bookshelf plugin hot paths found in CPU profiles | micro-optimizations | [Ghost 884502c](https://github.com/TryGhost/Ghost/commit/884502c945e595e54daba27c474d4923ab3a00b3) |
| Built the fallback meta context only when no other context exists | **+10% throughput** on a post page | [Ghost 4d029c4](https://github.com/TryGhost/Ghost/commit/4d029c48126c02802b7b1b65b886cddcc9ce0a85) |
| Removed duplicate `filterOptions` (contains `cloneDeep`) in models | one heavy call saved per model | [Ghost 26b7173](https://github.com/TryGhost/Ghost/commit/26b717319eec07175084decf7e107a16e40368be) |
| Reused one `toJSON()` result for offers | fewer serializations on a hot path | [Ghost 44edf3d](https://github.com/TryGhost/Ghost/commit/44edf3d379baea3a19357fdcb4faf2268c510a0a) |
| Hoisted a per-row `moment().subtract().format()` out of the attribution stats loop | removed the block from the flamegraph | [Ghost 23075b7](https://github.com/TryGhost/Ghost/commit/23075b7bf8a3b4f292db75661998ffe12e4e34d6) |
| Cleaned up a GA labs flag whose `isSet` check cost CPU on every request | small per-request win | [Ghost f8fbf57](https://github.com/TryGhost/Ghost/commit/f8fbf57a35774320afc431fcbbec97e3e8284f6a) |
| Logging hot paths: lodash → native | stdout logging ~13% faster | [framework 66cfc15](https://github.com/TryGhost/framework/commit/66cfc15628161297fa767dbdec7954b4d33a945d) |
| Logging timestamp: moment → `date-format` (with UTC offset handling) | **stdout logging 2× faster** | [framework be5ddf2](https://github.com/TryGhost/framework/commit/be5ddf25873826ae1ede06cae76f6c303eb32b73) |
| Sanitized the description once and passed it around | **−8% CPU per page** | [Gumroad #509](https://github.com/antiwork/gumroad/pull/509), [#475](https://github.com/antiwork/gumroad/pull/475) |
| Skipped computing descriptions on cards that don't display them | **~20% faster** Discover load | [Gumroad #683](https://github.com/antiwork/gumroad/pull/683), [#622](https://github.com/antiwork/gumroad/pull/622) |
| Adult-keyword detector: big `.match?` regex → `Set` lookup | 25% faster | [Gumroad #599](https://github.com/antiwork/gumroad/pull/599) |
| OTel `setAttributes`: `Object.entries` → `for...in` | faster span creation for every instrumentation | [opentelemetry-js #6514](https://github.com/open-telemetry/opentelemetry-js/pull/6514) |
| OTel `addEvent`/`addLink`: same fix + a counter instead of `Object.keys().length` | **9.5k → 76k ops/sec (8×)** | [opentelemetry-js #6516](https://github.com/open-telemetry/opentelemetry-js/pull/6516) |
| OTel span start: skip `_getTime` when no `startTime` is given | ~8–10% faster span creation | [opentelemetry-js #6528](https://github.com/open-telemetry/opentelemetry-js/pull/6528) |

### Algorithmic fixes

| Change | Result | Link |
| --- | --- | --- |
| Dynamic redirects: O(n²) ID array check → compute keys directly | ~3× faster | [Ghost bfdf2dd](https://github.com/TryGhost/Ghost/commit/bfdf2dd8bf079bbee9f1405fee7bf4a83128e0bc) |
| Recursive stats aggregation with concat/clone → single `reduce` (also fixed a stack overflow) | **85 ms → 1 ms** | [Ghost f250898](https://github.com/TryGhost/Ghost/commit/f250898a3b66948294e54ce52b71769e9d4a427b) |
| Theme package-name regex with nested quantifiers rewritten | **19,000 ms → 7 ms** | [gscan 1374e3f](https://github.com/TryGhost/gscan/commit/1374e3f70c221740123b6ded23e9cd44578ab988) |
| Precomputed normalized file paths once at theme load (it had been called tens of thousands of times) | 5 s → 1.7 s | [gscan b6d8e01](https://github.com/TryGhost/gscan/commit/b6d8e0192a0c7d845493c6c14a433874f891abcd) |
| Cached partial lookups across rules | less repeated work | [gscan f57ab07](https://github.com/TryGhost/gscan/commit/f57ab075c699a2f1a6fc65c45130097c942b1003) |
| Skipped an actor lookup when the actor is the same person | avoids an expensive N+1 | [ActivityPub aa34029](https://github.com/TryGhost/ActivityPub/commit/aa340294ab7222ce03bb44053095ad6110229bb8) |
| Sequential awaits in `profileGetAction` → `Promise.all` | removed the waterfall | [ActivityPub e15fc0f](https://github.com/TryGhost/ActivityPub/commit/e15fc0f6a1f7d7c36981c2afc43eb4c7b142e092) |
