# Database and query optimization

Use this for slow pages dominated by SQL, N+1 queries, slow migrations, slow DB
initialization or reset, query-planner surprises, and connection-pool limits.

## Techniques

- **Count queries first.** Count them per request, per boot or per init. Most wins
  below are "N queries → 1".
- **N+1 → preload.** In Rails use `includes`/`preload` for every association the
  view touches, including nested ones (thumbnail variants, attachment blobs, product
  variants). In ORMs generally, eager-load or batch-fetch by IDs.
- **Loop of SELECTs → one grouped query.** Fetch all rows once (or `GROUP BY` and
  count) and compare in memory.
- **Loop of INSERTs → batch insert** (`knex.batchInsert`, `insert_all`, multi-row
  `VALUES`).
- **Skip the re-read after writes.** On MySQL (no `RETURNING`), ORMs re-`SELECT`
  after save. Turn that off when the result is unused (Bookshelf
  `autoRefresh: false`), especially on huge tables or unindexed columns.
- **Rewrite expensive SQL shapes.** Use `EXISTS` instead of `IN (subquery)` or a join
  plus dedupe; it stops at the first match. Remove subqueries by rearranging the
  query.
- **Pin the plan when the planner flips.** Force the right index (`FORCE INDEX`) when
  MySQL starts choosing a worse one. Document why.
- **Fetch fewer columns or rows.** Exclude columns a hot service doesn't need (switch
  to an include list eventually). Shrink pagination when pages compute heavy data per
  row.
- **Migrations on big tables.** Use MySQL `ALGORITHM=COPY` for add/drop column where
  in-place is slow. Use raw SQL instead of ORM helpers that do slow foreign-key work.
  Lint migrations for performance rules and make the lint fail CI.
- **Initialization.** Get the list of existing tables once instead of `hasTable` per
  table (`information_schema` has no indexes). Fetch column and owner info only when
  there is something to insert.
- **Parallelize independent queries** (`Promise.all`) and size the connection pool
  for them. Validate pool changes against memory.
- **Upgrade drivers** when a faster maintained one exists (`mysql` → `mysql2`).
- **Driver-level fixes.** Hoist per-row work out of the row loop (for example, read
  column names once).

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| knex-migrator integrity check: one query per migration folder → one grouped query | **79 queries → 1** at every boot | [knex-migrator 838fe54](https://github.com/TryGhost/knex-migrator/commit/838fe5462837902bfdda0525bd8a292f57aa0d9a) |
| DB init: fetch existing migrations once instead of SELECT per migration | queries halved | [knex-migrator 8d9a561](https://github.com/TryGhost/knex-migrator/commit/8d9a561cab0b7b7f5745dd36bcfb7a06568d2311) |
| DB init: `batchInsert` for migration rows | ~470 fewer queries; 140 ms → 10 ms | [knex-migrator 38821c5](https://github.com/TryGhost/knex-migrator/commit/38821c52426f57a502b3ac859a2970bfa0b1e08f) |
| Default settings: batch insert, and fetch column/owner info only if needed | ~95 fewer queries at init, 3 fewer at boot | [Ghost 94f5add](https://github.com/TryGhost/Ghost/commit/94f5add1c0f19edf7f211df80faf28bb7fe996b5) |
| Table creation: one existence check instead of per table | 48 fewer queries | [Ghost ddaa4ee](https://github.com/TryGhost/Ghost/commit/ddaa4ee5e14efb6ede35eff1c02b6e0e52362baf) |
| `autoRefresh: false` when processing email batches (`email_recipients` is huge; `processed_at` is unindexed) | avoided giant re-SELECTs | [Ghost c4fe446](https://github.com/TryGhost/Ghost/commit/c4fe446c27c21c4cf0fa24a12aa41474b9229343) |
| `autoRefresh: false` for test fixture inserts | ~18,000 fewer queries in MySQL tests | [Ghost d127b97](https://github.com/TryGhost/Ghost/commit/d127b975afcefe9b3b1f3d11aa3ae55c04948a20) |
| Forced the ideal index on `members_newsletters` after a planner flip | restored fast newsletter counts | [Ghost 8debc4e](https://github.com/TryGhost/Ghost/commit/8debc4eb0296a5b1b73d63e73c903fccbd0b9f9c) |
| Migration utils always append `ALGORITHM=COPY` on MySQL | add/drop column on big tables made fast | [Ghost d8e58b5](https://github.com/TryGhost/Ghost/commit/d8e58b53d0984c198c2a6e12bd73b44356aab350) |
| Column rename via raw SQL on MySQL instead of the Knex helper | avoided slow FK handling on big clusters | [Ghost a699174](https://github.com/TryGhost/Ghost/commit/a6991746c3aec3e925e2d50f4e88edf87c8dcc74) |
| Excluded unneeded fields from URL-service fetches | faster URL service init | [Ghost f1638b8](https://github.com/TryGhost/Ghost/commit/f1638b869c96efbb48dbbc61c8be8851cab35840) |
| Settings cache: fewer adapter `get` calls | ~187 fewer calls at boot (matters with Redis) | [Ghost 6c2d70f](https://github.com/TryGhost/Ghost/commit/6c2d70f26a55af6197866bd007e4826dc4eaf992) |
| `mysql` → `mysql2` driver | faster, maintained, MySQL 8 auth | [Ghost bf6f607](https://github.com/TryGhost/Ghost/commit/bf6f607f424106c1973b09dd47f62c1b972b2ae6) |
| node-sqlite3: fetch column names once per statement | faster row materialization | [node-sqlite3 #1600](https://github.com/TryGhost/node-sqlite3/pull/1600) |
| Gumroad: `EXISTS` instead of a subquery for creator analytics products | slowest dashboard query **~3× faster** | [Gumroad #398](https://github.com/antiwork/gumroad/pull/398) |
| Gumroad: removed an expensive subquery by rearranging SQL | cheaper query | [Gumroad #391](https://github.com/antiwork/gumroad/pull/391) |
| Gumroad N+1 fixes via `preload`/`includes` (thumbnails, variants, blobs, blasts, customers page) | many queries → a few per page | [#385](https://github.com/antiwork/gumroad/pull/385), [#471](https://github.com/antiwork/gumroad/pull/471), [#478](https://github.com/antiwork/gumroad/pull/478), [#580](https://github.com/antiwork/gumroad/pull/580), [#600](https://github.com/antiwork/gumroad/pull/600), [#601](https://github.com/antiwork/gumroad/pull/601), [#656](https://github.com/antiwork/gumroad/pull/656), [#740](https://github.com/antiwork/gumroad/pull/740), [#765](https://github.com/antiwork/gumroad/pull/765) |
| Gumroad: pagination 100 → 25 emails, 5 → 3 past payouts | 4× less data to compute per page | [#581](https://github.com/antiwork/gumroad/pull/581), [#590](https://github.com/antiwork/gumroad/pull/590) |
| Gumroad: reverted an association-cache refactor that produced very heavy queries | restored scheduled-emails page | [#582](https://github.com/antiwork/gumroad/pull/582) |
| ActivityPub: parallelized all bulk fetches | no DB waterfall | [ActivityPub adeee44](https://github.com/TryGhost/ActivityPub/commit/adeee44ada7666c4de16ccacf3362bef62e76216) |
| ActivityPub: larger DB pool so parallel queries actually run concurrently. Reverted on a leak suspicion, then re-applied once ruled out | faster query-heavy requests | [80dab1c](https://github.com/TryGhost/ActivityPub/commit/80dab1ce0959d9e63fa9bd671c62548395e3ac23), [revert 59f02e0](https://github.com/TryGhost/ActivityPub/commit/59f02e09de264fdd65dac3bdaab0c1d37720263a), [re-apply 8b54d18](https://github.com/TryGhost/ActivityPub/commit/8b54d189d4fba2b998d658ad9f7ecfde4ae1caf3) |
