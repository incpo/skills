# CI, build, test-suite and dev-loop speed

Use this for slow pipelines, slow test suites, slow local builds/watch mode, and flaky
slowness. Treat developer time as a product metric. Many of these are high-ROI
one-liners.

## Techniques

### Do less
- Run tests and lint only for changed packages (`--since`, Nx affected).
- Cancel superseded runs (`concurrency: cancel-in-progress`).
- Drop redundant jobs that newer pipelines already cover.
- Don't install everything: only the Playwright browsers you test, and skip
  submodule installs you don't need.
- Disable apt `man-db` triggers on runners (saves 1–2 min).

### Cache
- Dependency caches with correct keys (reset them when new workspace packages appear).
- `yarn --prefer-offline`.
- Nx/Turbo task caching with declared `outputs` and env `inputs`, persisted across
  commits.
- `eslint --cache` plus cached `.eslintcache`.
- Playwright browser cache.
- Persistent webpack filesystem cache (2× faster warm builds).

### Parallelize sensibly
- Run independent suites in separate jobs, and run Playwright with several workers.
- Run `tsc` alongside the bundler.
- Raise Nx parallelism to match cores, but don't stack `concurrently` on top of Nx.
  Oversubscribing cores made builds slower.
- Merge tiny sequential steps (each new GitHub job costs ~10–12 s to start).

### Make tests cheap (gated to the test environment)
- Minimal bcrypt rounds.
- Copy a pristine SQLite file instead of re-migrating, or truncate/`DELETE` and
  re-insert fixtures instead of dropping the DB. `DELETE FROM` beat `TRUNCATE` on
  small MySQL tables.
- SQLite on tmpfs (`os.tmpdir()`); `journal_mode=TRUNCATE`.
- Disable throttling, retries and background jobs that only add waiting time in
  tests.
- Mock slow `exec`/spawn and network calls; use fake timers instead of `setTimeout`.
- Reuse browser pages across E2E tests.

### Tooling
- Restrict `tsconfig` `types`/`lib` (2× faster `tsc`).
- Call Nx directly instead of going through `yarn` (hundreds of ms per call).
- Fix script names that cause double builds.
- Lint only staged files in git hooks (`lint-staged`).

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| bcrypt rounds → 1 in tests (≈300 hashes per run) | 140 ms → 3 ms per hash | [Ghost 0a68ea8](https://github.com/TryGhost/Ghost/commit/0a68ea88fb5f1135afad612f0701ff48743a7651) |
| Copy a clean SQLite DB file on reset instead of migrator reset+init | ~700 ms saved per reset | [Ghost a3cc66b](https://github.com/TryGhost/Ghost/commit/a3cc66be50af0419cbe7970299d08fab8f5d3533) |
| SQLite test DB in `/tmp` | 2× faster SQLite suite | [Ghost db1c1ce](https://github.com/TryGhost/Ghost/commit/db1c1ce21d6ec6f26bd6225c1d62925239be6552) |
| `DELETE FROM` instead of `TRUNCATE` for MySQL resets | reset 300 ms → 30 ms (−90%) | [Ghost 99aeb73](https://github.com/TryGhost/Ghost/commit/99aeb73ecc983a799016bbb599362a88a5c1d4c6) |
| Raised Stripe throttling limits in tests | removed **20% of E2E time** spent waiting | [Ghost 463d6e0](https://github.com/TryGhost/Ghost/commit/463d6e0631e16dfb7e7c7966c4688df4c481393d) |
| Disabled the members migrations job in tests (500 ms wait × 55 boots) | −27 s idle | [Ghost bf63e25](https://github.com/TryGhost/Ghost/commit/bf63e250ad98e9669899ddcc3e5162fdcd2b9240) |
| Stubbed `exec` in tests | −650 ms in one file | [Ghost f4ffd39](https://github.com/TryGhost/Ghost/commit/f4ffd394206156ecafdc9657339e5bd6a3082493) |
| Fake timers instead of `setTimeout` in scheduler tests | no real waits | [Ghost c2c986f](https://github.com/TryGhost/Ghost/commit/c2c986f1e38fac23b9c3673ff7e2e9d3a00b93bc) |
| Reused Playwright pages across tests | −1 min CI | [Ghost 27fc2d2](https://github.com/TryGhost/Ghost/commit/27fc2d2fbbe75de5914e54ab5258438176781403) |
| Prevented `man-db` refresh after Playwright deps install | −up to 2 min | [Ghost 23a9937](https://github.com/TryGhost/Ghost/commit/23a99372e98096b64d8cd3df09a64b1bae3aa60b) |
| `yarn` defaults to `--prefer-offline` | no network round-trip per dependency | [Ghost c25a2ad](https://github.com/TryGhost/Ghost/commit/c25a2adf606bfa7ab4c3686089baf7173736eb92) |
| Restricted `tsconfig` `types` and `lib` | `tsc` ~2× faster | [Ghost 804c1e4](https://github.com/TryGhost/Ghost/commit/804c1e4cbb0b93d9499b8db1550bb36e7f5b15f3) |
| Removed `concurrently` from app builds under Nx (core oversubscription) | faster individual builds | [Ghost 33dd6b6](https://github.com/TryGhost/Ghost/commit/33dd6b6b495360e3f97c6e2e594294af393a74d0) |
| Merged CI setup steps | saves 10–12 s job start-up each | [Ghost 5791be4](https://github.com/TryGhost/Ghost/commit/5791be4937e923f20fe7845068cada6d4a1e48bb) |
| Unit tests only for changed packages | −~3 min per run | [Ghost 40e8bc0](https://github.com/TryGhost/Ghost/commit/40e8bc09ced8ec6733022896f1ca48a2a659e15d) |
| Nx build cache kept across commits on `main` | no rebuilding unchanged TS projects | [Ghost 7c346c2](https://github.com/TryGhost/Ghost/commit/7c346c28eb74c7f8e71b82dd71765a80d96837dc) |
| `eslint --cache` | lint only changed files | [Ghost 880f8c2](https://github.com/TryGhost/Ghost/commit/880f8c2802e0808b3ce968ca2b3fee70fef3a0ca) |
| Persistent webpack filesystem cache for Ember | warm build 2× faster | [Ghost bba95ce](https://github.com/TryGhost/Ghost/commit/bba95ce03439d02e82eb03233c7e5f64ff6c71ad) |
| Fixed long CI delay from a 5-minute healthcheck interval | faster CI startup | ActivityPub 3a7ca5d ("Fixed long delay in CI tests") |
