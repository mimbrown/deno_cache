# deno_cache

A TypeScript implementation of the DENO_DIR/cache for the Deno CLI.

This is designed to provide access to the cache using very similar logic to the
way that the Deno CLI accesses the cache, which allows items like
[`deno_graph`](), [`deno_doc`](), `deno_check`, and `deno_emit` to access and
populate the cache in the same way that the CLI does.

## Permissions

Because of the nature of code, it requires several permissions to be able to
work properly. If the permissions aren't granted at execution, the code will try
to prompt for them, only requesting what is specifically needed to perform the
task.

- `--allow-env` - The code needs access to several environment variables,
  depending on the platform as well, these can include `HOME`, `USERPROFILE`,
  `LOCALAPPDATA`, `XDG_CACHE_HOME`, `DENO_DIR`, and `DENO_AUTH_TOKENS`.
- `--allow-read` - In certain cases the code needs to determine the current
  working directory, as well as read the cache files, so it requires read
  permission.
- `--allow-write` - The code requires write permission to the root of the cache
  directory.
- `--allow-net` - The code requires net access to any remote modules that are
  not found in the cache.

This can just be granted on startup to avoid being prompted for them.

## Example

Using the cache and the file fetcher to provide modules to build a module graph:

```ts
import { createCache } from "https://deno.land/x/deno_cache/mod.ts";
import { createGraph } from "https://deno.land/x/deno_graph/mod.ts";

// create a cache where the location will be determined environmentally
const cache = createCache();
// destructuring the two functions we need to pass to the graph
const { cacheInfo, load } = cache;
// create a graph that will use the cache above to load and cache dependencies
const graph = await createGraph("https://deno.land/x/oak@v9.0.1/mod.ts", {
  cacheInfo,
  load,
});

// log out the console a similar output to `deno info` on the command line.
console.log(graph.toString());
```
