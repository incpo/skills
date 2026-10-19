# Startup, cold start and module loading

Use this for slow boot, slow serverless or container cold starts, slow CLI start-up,
or high baseline memory caused by loading code that the first request doesn't need.

## Techniques

- **Lazy-require heavy, niche dependencies** inside the function that uses them
  (HTML parsers, markdown, oEmbed, scrapers, SDKs). Wrap repeated lazy requires in a
  small helper to keep code DRY.
- **Lazy-mount routes and subsystems.** Mount old API versions or rarely used routers
  on first hit (for example, `express-lazy-router`).
- **Gate unused frameworks or instrumentation** behind config or env vars. Only load
  OpenTelemetry/APM/profilers when they are enabled. Remove agents you no longer use.
- **Import only what you use.** Use deep or ESM imports (`date-fns/format`,
  `lodash-es`) so you don't load or parse whole packages.
- **Defer work from boot to first use.** Lazy-minify assets at first request with
  cache invalidation. Don't precompute caches nobody reads at boot.
- **Native over pure-JS** for crypto and keygen.
- **Bundle the server** (esbuild into one file) instead of runtime transpilers such as
  `tsx`/`ts-node` in production.
- **Reduce startup DB/IO.** See [database.md](database.md) (migrations integrity
  check, settings fetch).
- **Watch for regressions after refactors.** Extracting code into a package can
  silently turn a lazy require back into an eager one.

Trade-off he names explicitly: scattering requires obscures code a little, so do it
where the measured win is real, and keep the changes small.

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| Lazy-loaded API routes (v2/v3/canary) via `lazyUse` + `express-lazy-router` | **−18% boot time, −6% initial memory** | [Ghost f4fb0fc](https://github.com/TryGhost/Ghost/commit/f4fb0fcbaa6dd98b2da5ac85e5d1451340f302ba) |
| Inlined niche package requires across core | **boot −31%, initial memory −12%** (vs 4.19.0) | [Ghost ed6a8dc](https://github.com/TryGhost/Ghost/commit/ed6a8dca76d33d0b43f865bf9f006fd4a93bb2ba) |
| Lazy-loaded OpenTelemetry unless instrumentation is enabled | avoided a **2× boot regression** | [Ghost 12cbb22](https://github.com/TryGhost/Ghost/commit/12cbb22b8512478c191dada145904bf0fc16a354) |
| Gated the unused NestJS framework behind an env var | −6–9% boot | [Ghost 0a6cd75](https://github.com/TryGhost/Ghost/commit/0a6cd75993c4315a4d4ba25275a34bfbd58c9f8c) |
| Removed NewRelic after moving to Sentry | −200–300 ms | [Ghost 48ae822](https://github.com/TryGhost/Ghost/commit/48ae822b9fd944ae6d9dabded3e6bcf4dbbd21aa) |
| Fixed `date-fns` whole-package require (via NQL bump) | −5% boot | [Ghost de5dcb8](https://github.com/TryGhost/Ghost/commit/de5dcb81895e9b5e7bd44c134f1871d6f00616c6) |
| Lazy-minified theme assets at first request | no minification at boot | [Ghost b092929](https://github.com/TryGhost/Ghost/commit/b092929bba77ab386e8095052ab0635da96f0656) |
| Lazy-loaded `@extractus/oembed-extractor` | several MB RAM saved per instance | [Ghost 898b552](https://github.com/TryGhost/Ghost/commit/898b55265c555c195d80caed7a58e1d7b705b07f) |
| Lazy-loaded jsdom | less boot CPU and memory | [Ghost 8fd32f0](https://github.com/TryGhost/Ghost/commit/8fd32f012b2aed8235aaa20bd35ff6e2c8bc1714) |
| Restored a lazy `mailgun-js` require lost during package extraction | fixed a boot regression | [Ghost 2a07b94](https://github.com/TryGhost/Ghost/commit/2a07b94bd4491bb8f30cb32334efda045babaf61) |
| Deferred loading metascraper | boot 1.47 s → 1.33 s | [Ghost b91f307](https://github.com/TryGhost/Ghost/commit/b91f3071be893c7092541855c023a34a496f5d02) |
| JS RSA keypair generator → native `rsa-keypair` | faster boot | [Ghost 6473569](https://github.com/TryGhost/Ghost/commit/64735693beda5800bc81fdcd610d71a84ca36896) |
| knex-migrator: list migration file names instead of `require`-ing every migration | −5% on-stack boot time | [knex-migrator c016a3a](https://github.com/TryGhost/knex-migrator/commit/c016a3a1b6d9fe2f3bfb89653241246d6a66e970) |
| Lazy-loaded instrumentation modules (only one is needed at a time) | faster boot | [ActivityPub 80d0093](https://github.com/TryGhost/ActivityPub/commit/80d009368b5c2df61cfd8878a680f2d795355f61) |
| Removed redundant Knex instrumentation (mysql2 already instrumented) | less boot bloat | [ActivityPub db2bf2e](https://github.com/TryGhost/ActivityPub/commit/db2bf2e4e22014be7c02327f8770a7d0bf153c8c) |
| `tsx` → esbuild single-file bundle | fixed slow boot | [ActivityPub c8f5322](https://github.com/TryGhost/ActivityPub/commit/c8f53222d5b29f809b5a3c35dd2af7ce7a933b25) |
| Replaced `fs-extra` with native `fs` in the logging lib | leaner script startup | [framework 656b480](https://github.com/TryGhost/framework/commit/656b4800c7d6e1831353c7755fa2e2dfa4a49068) |
