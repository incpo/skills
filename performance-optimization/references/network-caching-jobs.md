# External calls, caching and background jobs

Use this when page time is dominated by third-party APIs (payments, storage, search),
repeated lookups, client-side extra requests, or when work should move to or be
scheduled better in background jobs.

## Techniques

- **Delete calls whose results are unused.** Trace each outbound call to where its
  value is consumed. Often a different code path already supplies it.
- **Skip calls that can't succeed** (a resource that doesn't exist for this user or
  region). Failed calls are often not cached either.
- **Don't fire empty requests from the client** (for example, a POST with an empty ID
  list).
- **Memoize per request or per object** when two code paths call the same API.
- **Inline data into the page** instead of secondary API requests after load. Remove
  the leftover fetches once inlining lands.
- **Persistent connections.** Use an HTTP client with keep-alive and pooling (Ruby:
  `typhoeus` under Faraday for Elasticsearch).
- **Precompute heavy aggregates in background jobs** and serve from cache:
  - choose the cache-eligibility rule by data size (sales count), not by unrelated
    signals (last sign-in)
  - tune thresholds from evidence (100k → 50k sales)
  - keep a worker that refreshes the size metric, or nothing ever qualifies
- **Schedule jobs efficiently.** Use bulk enqueue (`Sidekiq::Client.push_bulk`). Avoid
  scheduling gaps where uniqueness locks block fresher jobs. Shorten intervals when
  the queue spills into the next day.
- **CDN and browser caching.** Use content-hashed assets with long TTLs, and shorter
  TTLs for mutable CDN endpoints. Guard purge steps so you don't hit purge rate limits.

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| Skipped a PayPal API call on dashboard load whose value was never used | **−500 ms** dashboard load | [Gumroad #300](https://github.com/antiwork/gumroad/pull/300) |
| Skipped an S3 HEAD for the 1099 form for non-US users (it always failed, uncached) | removed a slow request per load | [Gumroad #527](https://github.com/antiwork/gumroad/pull/527) |
| Removed 4 secondary balance/stat requests whose results were unused after inlining | fewer requests, simpler code | [Gumroad #356](https://github.com/antiwork/gumroad/pull/356) |
| Cached the Stripe instant-payout eligibility check called from 2 paths | one Stripe call instead of two | [Gumroad #348](https://github.com/antiwork/gumroad/pull/348), [#589](https://github.com/antiwork/gumroad/pull/589) |
| Called `custom_context` once instead of twice | −~4 DB queries per logged-in page (~2%) | [Gumroad #307](https://github.com/antiwork/gumroad/pull/307) |
| Computed analytics products once in the caching proxy instead of per date range | query cache now effective | [Gumroad #397](https://github.com/antiwork/gumroad/pull/397), [#326](https://github.com/antiwork/gumroad/pull/326) |
| Added `typhoeus` for pooled, persistent HTTP connections | no TLS/TCP setup per Elasticsearch call | [Gumroad #289](https://github.com/antiwork/gumroad/pull/289) |
| Balance-stats cache: eligibility by size (dropped last-sign-in check), threshold 100k → 50k, worker to refresh `sales_count` | slow dashboards for mid-size sellers cached | [#301](https://github.com/antiwork/gumroad/pull/301), [#315](https://github.com/antiwork/gumroad/pull/315), [#302](https://github.com/antiwork/gumroad/pull/302) |
| Sidekiq: `push_bulk` for per-seller sub-jobs; removed scheduling gaps; shorter intervals | fresher stats, cheaper enqueue | [#361](https://github.com/antiwork/gumroad/pull/361), [#353](https://github.com/antiwork/gumroad/pull/353), [#308](https://github.com/antiwork/gumroad/pull/308) |
| Comment-count script no longer POSTs when there are no post IDs | one less request per page | [Ghost 7ac0fdb](https://github.com/TryGhost/Ghost/commit/7ac0fdb23d3621f84692a13c3b50e675dd92bb94) |
| API framework computes headers only when they are used | less per-request work | [Ghost cb7cfdb](https://github.com/TryGhost/Ghost/commit/cb7cfdb4deea97f074a0940b980e9104a45a6e8c) |
| Settings cache `get`s reduced (prep for Redis-backed cache) | ~187 fewer adapter calls at boot | [Ghost 6c2d70f](https://github.com/TryGhost/Ghost/commit/6c2d70f26a55af6197866bd007e4826dc4eaf992) |
| Avoided loading subscription events when the threshold is `Infinity` | early return before I/O | Ghost a9b011a ("Avoided loading newsletter subscription events if threshold is Infinity") |
