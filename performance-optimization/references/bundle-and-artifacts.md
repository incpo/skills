# Bundle, dependency, package and image size

Use this for large frontend bundles, slow compile times caused by huge imports, heavy
`node_modules`/install size, bloated published packages, and large Docker images.

## Techniques

- **Make tree-shaking actually work.** Use `lodash-es` or per-method imports
  (`lodash/debounce`). Use the library's documented tree-shakable import path (for
  example, `validator/es/lib/isEmail`). Import submodules of `date-fns` etc.
- **Swap heavy transitive dependencies** for the small sub-package you actually use
  (`@stdlib/utils` → `@stdlib/copy`).
- **Keep dev tools out of `dependencies`** (prettier, linters, test libs).
- **Publish only what's needed.** Use a `files` array or `.npmignore` so coverage
  reports and fixtures don't ship. Watch for ignore regexes that accidentally drop
  real assets.
- **Unpin library dependencies** so consumers dedupe to one version (applications
  keep lockfiles).
- **Docker:**
  - slim base images (`bookworm-slim`)
  - delete the package-manager cache (`yarn cache clean`, `rm -rf /var/lib/apt/lists/*`)
    in the same layer
  - check efficiency with `dive`
  - drop Buildx layer caching when pulling fresh is faster than importing the cache
- **Cache-bust built assets** (content hashes) so long cache TTLs are safe.

## Real examples

| Change | Result | Link |
| --- | --- | --- |
| `lodash` → `lodash-es` in AdminX apps | **−500 KB per app**, faster compiles | [Ghost 7db1cb4](https://github.com/TryGhost/Ghost/commit/7db1cb4f140e1b3248a4c9c475267ef9d3068af6) |
| Tree-shakable `validator` import | −75 KB | [Ghost f96313e](https://github.com/TryGhost/Ghost/commit/f96313e9c2deaa5a0f3f282333efc69160c0588b) |
| Gumroad: explicit lodash submodule imports | −~200 KB bundle | [Gumroad #359](https://github.com/antiwork/gumroad/pull/359) |
| `@tryghost/errors`: `@stdlib/utils` → `@stdlib/copy` | **−140 MB+ of dependencies** | [Ghost 6529bcf](https://github.com/TryGhost/Ghost/commit/6529bcf3b13e0f9e83a88494d28393620e5af262) |
| `@tryghost/limit-service` stopped importing all of lodash | smaller bundle | Ghost d46aab5 ("Optimized bundle size of `@tryghost/limit-service`") |
| Moved `prettier` to devDependencies in the Lexical renderer | consumers don't install prettier | [Ghost 72ce751](https://github.com/TryGhost/Ghost/commit/72ce7511b7ff562392482b3162e3fc0e705dc654) |
| Added a `files` array so coverage isn't published | smaller package | Ghost 5257451 ("Added `files` array to html-to-mobiledoc") |
| Docker: removed the Yarn cache after install | **−900 MB** image | [ActivityPub b94e326](https://github.com/TryGhost/ActivityPub/commit/b94e326d48381d0dc9665f7878b22696023fe78a) |
| Docker: `bookworm-slim` + removed the apt cache | −100 MB, 99% `dive` efficiency | [ActivityPub 6ff1fab](https://github.com/TryGhost/ActivityPub/commit/6ff1fabab63e44f4f7378920bbbdcb0f5a1fe3e7) |
| Removed Docker Buildx (layer-cache import slower than a fresh pull) | faster CI image builds | [ActivityPub d690614](https://github.com/TryGhost/ActivityPub/commit/d6906146efad30d201c8a2c3a960abb4ade70221) |
