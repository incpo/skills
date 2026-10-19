# Memory usage, leaks and fragmentation

Use this for high RSS, memory that grows and doesn't come back, OOM kills, or reducing
per-instance memory to fit more instances per host.

## Techniques

- **Measure RSS and heap separately.** If a heap snapshot "shows nothing in JS code"
  while RSS grows, suspect native libraries (image processing, compression) and
  allocator fragmentation.
- **Turn off caches that never hit.** Library-internal caches (for example, libvips
  in Sharp) cost memory without benefit when each input is processed once.
- **Limit native concurrency** (Sharp/libvips threads) to reduce fragmentation. Run
  with `jemalloc` (`LD_PRELOAD`) in production.
- **Don't load what you don't use.** Lazy-requiring heavy dependencies cuts baseline
  memory (see [startup-and-loading.md](startup-and-loading.md)).
- **Drop process wrappers.** `npx foo` spawns an extra Node process (~80 MB). Call
  the binary directly from `yarn`/`npm` scripts or `PATH`.
- **Stop listener and event pile-up.** Register handlers once. Reset singletons
  between test boots.
- **Bound unbounded arrays.** Something pushed to on every init will grow forever.
- **In C/C++:** free on early-return paths and run Valgrind/ASan.
- **Be careful attributing leaks.** Correlation with a deploy isn't proof. Revert to
  test the theory, and re-apply if it was innocent.

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| Disabled Sharp's 50 MB internal cache (Ghost never optimizes the same image twice) | 10 uploads: **peak 480 → 270 MB, settled 330 → 161 MB** | [Utils 03d24ea](https://github.com/TryGhost/Utils/commit/03d24ea4fea697d99c2e9dc3c00af1cc6eecfc16), [Ghost 22be7d0](https://github.com/TryGhost/Ghost/commit/22be7d0ae703294b053b975c92ce313481531156) |
| Limited Sharp concurrency to reduce fragmentation (on top of jemalloc) | addresses memory hogging after image uploads | [Ghost 51b8bbd](https://github.com/TryGhost/Ghost/commit/51b8bbd38c39a921ef83d7e5c5c85dc14095ff7c) |
| Lazy routes + lazy requires | initial memory −6% and −12% | [Ghost f4fb0fc](https://github.com/TryGhost/Ghost/commit/f4fb0fcbaa6dd98b2da5ac85e5d1451340f302ba), [Ghost ed6a8dc](https://github.com/TryGhost/Ghost/commit/ed6a8dca76d33d0b43f865bf9f006fd4a93bb2ba) |
| Dropped the `npx` prefix from `concurrently` commands | **~80 MB RAM saved** in dev and prod | [hyperdx #1505](https://github.com/hyperdxio/hyperdx/pull/1505) |
| Actions model pushed to a `candidates` array on every init, growing to thousands of entries | fixed "maximum call stack exceeded" | Ghost 2c60340 ("Fixed maximum call stack exceeded error when filtering Action events") |
| Reset the URL service between test boots, and registered the XML-RPC listener once | listeners stopped piling up (~3% of test time) | [Ghost 264773c](https://github.com/TryGhost/Ghost/commit/264773ccd17f54459429dde4fcf09678019f5304), [Ghost 4a9575d](https://github.com/TryGhost/Ghost/commit/4a9575d179f750b4e13db07dbde037ce3f3852f8) |
| DB pool increase reverted on leak suspicion, then re-applied ("not the cause of the memory leak") | evidence-based revert | [ActivityPub 59f02e0](https://github.com/TryGhost/ActivityPub/commit/59f02e09de264fdd65dac3bdaab0c1d37720263a), [8b54d18](https://github.com/TryGhost/ActivityPub/commit/8b54d189d4fba2b998d658ad9f7ecfde4ae1caf3) |
| C leak fixes: free on early return / unfreed allocations | no leaks under Valgrind | [sway #614](https://github.com/swaywm/sway/pull/614), [i3 #2343](https://github.com/i3/i3/pull/2343), [termite #346](https://github.com/thestinger/termite/pull/346), [nginx/unit #29](https://github.com/nginx/unit/pull/29) |
