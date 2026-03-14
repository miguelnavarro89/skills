---
name: bunjs
description: Use Bun as runtime, package manager, bundler, and test runner. Use when running JS/TS with bun, installing packages with bun install, bundling with bun build, testing with bun test, or when user mentions Bun, bunfig.toml, or bun.lock.
---

# Bun.js

All-in-one JavaScript/TypeScript toolkit: runtime, package manager, bundler, test runner.

## Quick Reference

```bash
# Runtime
bun run index.ts          # Execute file (TS/JSX native)
bun --watch index.ts      # Watch mode
bun --hot index.ts        # Hot reload

# Package Manager
bun install               # Install deps (25x faster than npm)
bun add <pkg>             # Add dependency
bun add -d <pkg>          # Add dev dependency
bun remove <pkg>          # Remove dependency
bun update                # Update deps
bunx <pkg>                # Execute package (like npx)

# Bundler
bun build ./src --outdir ./dist
bun build --minify --sourcemap linked

# Test Runner
bun test                  # Run tests
bun test --watch          # Watch mode
bun test --coverage       # With coverage
```

## Runtime

### Execution

```bash
bun run script.ts         # Run file
bun run start             # Run package.json script
bun --bun run start       # Force Bun runtime (not Node)
```

### Built-in APIs

```ts
// HTTP Server
Bun.serve({
    port: 3000,
    fetch(req) {
        return new Response("Hello!");
    },
});

// File I/O
const file = Bun.file("./data.json");
const text = await file.text();
await Bun.write("out.txt", "content");

// SQLite (native)
import { Database } from "bun:sqlite";
const db = new Database("mydb.sqlite");

// Hashing
const hash = Bun.hash("data");
const password = await Bun.password.hash("secret");

// Shell
import { $ } from "bun";
await $`ls -la`;
```

### Environment Variables

```ts
// Auto-loads .env files
const apiKey = Bun.env.API_KEY;
// or
const apiKey = process.env.API_KEY;
```

## Package Manager

### Commands

| Command | Description |
|---------|-------------|
| `bun install` | Install all deps |
| `bun add <pkg>` | Add dependency |
| `bun add -d <pkg>` | Add dev dependency |
| `bun add -g <pkg>` | Add global |
| `bun remove <pkg>` | Remove |
| `bun update` | Update all |
| `bun outdated` | Check outdated |

### Lockfile

- `bun.lock` - Text lockfile (v1.2+)
- `bun.lockb` - Binary lockfile (legacy)

### CI/CD

```bash
bun ci                    # Frozen lockfile install
bun install --frozen-lockfile
```

### Workspaces

```json
{
    "workspaces": ["packages/*"]
}
```

## Bundler

### Basic Usage

```ts
// JavaScript API
await Bun.build({
    entrypoints: ["./src/index.ts"],
    outdir: "./dist",
    target: "browser", // "browser" | "bun" | "node"
    minify: true,
    sourcemap: "linked",
});
```

```bash
# CLI
bun build ./src/index.ts --outdir ./dist --minify
```

### Options

| Option | Description |
|--------|-------------|
| `--target` | `browser`, `bun`, `node` |
| `--format` | `esm`, `cjs`, `iife` |
| `--minify` | Enable minification |
| `--sourcemap` | `none`, `linked`, `inline`, `external` |
| `--splitting` | Code splitting |
| `--external` | Exclude packages |

### Standalone Executables

```bash
bun build ./cli.ts --compile --outfile mycli
./mycli
```

## Test Runner

### Writing Tests

```ts
import { test, expect, describe, beforeAll, mock } from "bun:test";

describe("math", () => {
    test("adds numbers", () => {
        expect(2 + 2).toBe(4);
    });

    test("async test", async () => {
        const result = await fetchData();
        expect(result).toBeDefined();
    });
});
```

### Mocking

```ts
import { mock, spyOn } from "bun:test";

const fn = mock(() => 42);
fn();
expect(fn).toHaveBeenCalled();

// Spy on object method
const spy = spyOn(console, "log");
```

### CLI Options

```bash
bun test                          # Run all
bun test --watch                  # Watch mode
bun test --coverage               # Coverage
bun test -t "pattern"             # Filter by name
bun test --timeout 10000          # Set timeout
bun test --bail                   # Stop on first failure
bun test --update-snapshots       # Update snapshots
```

## Configuration (bunfig.toml)

```toml
# Runtime
preload = ["./setup.ts"]
logLevel = "debug"

[define]
"process.env.NODE_ENV" = "'production'"

# Package Manager
[install]
production = false
frozenLockfile = false
exact = false

[install.scopes]
myorg = { token = "$NPM_TOKEN", url = "https://registry.myorg.com/" }

# Test Runner
[test]
root = "./__tests__"
preload = ["./test-setup.ts"]
coverage = true
coverageThreshold = 0.8
```

## Framework Integration

### Next.js

```bash
bunx create-next-app my-app
cd my-app && bun dev
```

### React

```bash
bun create react my-app
```

### Express

```ts
import express from "express";
const app = express();
app.listen(3000);
```

## TypeScript

Native support, no config needed. For types:

```bash
bun add -d @types/bun
```

## Node.js Compatibility

Bun aims for Node.js compatibility. Most `node:*` modules work:
- `node:fs`, `node:path`, `node:http`, `node:crypto`, etc.

Check [compatibility status](https://bun.sh/docs/runtime/nodejs-compat) for specifics.

## Documentation References

### Core

- [Welcome to Bun](https://bun.com/docs/index.md) - Overview and getting started
- [Installation](https://bun.com/docs/installation.md) - Install methods (npm, Homebrew, Docker)
- [Quickstart](https://bun.com/docs/quickstart.md) - Build your first app

### Runtime

- [Runtime overview](https://bun.com/docs/runtime/index.md)
- [Bun APIs](https://bun.com/docs/runtime/bun-apis.md) - Native APIs on Bun global
- [bunfig.toml](https://bun.com/docs/runtime/bunfig.md) - Configuration file
- [Environment Variables](https://bun.com/docs/runtime/environment-variables.md)
- [Module Resolution](https://bun.com/docs/runtime/module-resolution.md)
- [TypeScript](https://bun.com/docs/typescript.md) | [JSX](https://bun.com/docs/runtime/jsx.md)
- [Watch Mode](https://bun.com/docs/runtime/watch-mode.md) - --watch and --hot
- [Node.js Compatibility](https://bun.com/docs/runtime/nodejs-compat.md)
- [Globals](https://bun.com/docs/runtime/globals.md) | [Web APIs](https://bun.com/docs/runtime/web-apis.md)
- [Debugging](https://bun.com/docs/runtime/debugger.md) - WebKit Inspector

#### Built-in Modules

- [File I/O](https://bun.com/docs/runtime/file-io.md) | [SQLite](https://bun.com/docs/runtime/sqlite.md) | [Redis](https://bun.com/docs/runtime/redis.md) | [S3](https://bun.com/docs/runtime/s3.md) | [SQL](https://bun.com/docs/runtime/sql.md)
- [Shell](https://bun.com/docs/runtime/shell.md) | [Hashing](https://bun.com/docs/runtime/hashing.md) | [Glob](https://bun.com/docs/runtime/glob.md)
- [Streams](https://bun.com/docs/runtime/streams.md) | [Workers](https://bun.com/docs/runtime/workers.md) | [FFI](https://bun.com/docs/runtime/ffi.md) | [Spawn](https://bun.com/docs/runtime/child-process.md)

#### HTTP & Networking

- [HTTP Server](https://bun.com/docs/runtime/http/server.md) | [Routing](https://bun.com/docs/runtime/http/routing.md) | [WebSockets](https://bun.com/docs/runtime/http/websockets.md)
- [TLS](https://bun.com/docs/runtime/http/tls.md) | [Cookies](https://bun.com/docs/runtime/http/cookies.md) | [Fetch](https://bun.com/docs/runtime/networking/fetch.md)
- [TCP](https://bun.com/docs/runtime/networking/tcp.md) | [UDP](https://bun.com/docs/runtime/networking/udp.md) | [DNS](https://bun.com/docs/runtime/networking/dns.md)
- [HTMLRewriter](https://bun.com/docs/runtime/html-rewriter.md) | [Transpiler](https://bun.com/docs/runtime/transpiler.md)

### Package Manager

- [install](https://bun.com/docs/pm/cli/install.md) | [add](https://bun.com/docs/pm/cli/add.md) | [remove](https://bun.com/docs/pm/cli/remove.md) | [update](https://bun.com/docs/pm/cli/update.md) | [link](https://bun.com/docs/pm/cli/link.md)
- [publish](https://bun.com/docs/pm/cli/publish.md) | [bunx](https://bun.com/docs/pm/bunx.md) | [outdated](https://bun.com/docs/pm/cli/outdated.md) | [audit](https://bun.com/docs/pm/cli/audit.md) | [why](https://bun.com/docs/pm/cli/why.md) | [patch](https://bun.com/docs/pm/cli/patch.md)
- [Lockfile](https://bun.com/docs/pm/lockfile.md) | [Workspaces](https://bun.com/docs/pm/workspaces.md) | [Catalogs](https://bun.com/docs/pm/catalogs.md) | [Global cache](https://bun.com/docs/pm/global-cache.md)
- [Isolated installs](https://bun.com/docs/pm/isolated-installs.md) | [Lifecycle scripts](https://bun.com/docs/pm/lifecycle.md) | [Scopes/registries](https://bun.com/docs/pm/scopes-registries.md)
- [Overrides](https://bun.com/docs/pm/overrides.md) | [.npmrc](https://bun.com/docs/pm/npmrc.md) | [--filter](https://bun.com/docs/pm/filter.md)

### Bundler

- [Overview](https://bun.com/docs/bundler/index.md) | [Loaders](https://bun.com/docs/bundler/loaders.md) | [Plugins](https://bun.com/docs/bundler/plugins.md) | [Executables](https://bun.com/docs/bundler/executables.md)
- [CSS](https://bun.com/docs/bundler/css.md) | [HTML & static sites](https://bun.com/docs/bundler/html-static.md) | [Fullstack dev server](https://bun.com/docs/bundler/fullstack.md)
- [Hot reloading](https://bun.com/docs/bundler/hot-reloading.md) | [Macros](https://bun.com/docs/bundler/macros.md) | [Minifier](https://bun.com/docs/bundler/minifier.md) | [Bytecode](https://bun.com/docs/bundler/bytecode.md)
- [esbuild migration](https://bun.com/docs/bundler/esbuild.md)

### Test Runner

- [Overview](https://bun.com/docs/test/index.md) | [Writing tests](https://bun.com/docs/test/writing-tests.md) | [Mocks](https://bun.com/docs/test/mocks.md) | [Snapshots](https://bun.com/docs/test/snapshots.md)
- [Lifecycle hooks](https://bun.com/docs/test/lifecycle.md) | [DOM testing](https://bun.com/docs/test/dom.md) | [Code coverage](https://bun.com/docs/test/code-coverage.md)
- [Configuration](https://bun.com/docs/test/configuration.md) | [Finding tests](https://bun.com/docs/test/discovery.md) | [Dates/times](https://bun.com/docs/test/dates-times.md) | [Reporters](https://bun.com/docs/test/reporters.md)

### Framework Guides

- [Next.js](https://bun.com/docs/guides/ecosystem/nextjs.md) | [React](https://bun.com/docs/guides/ecosystem/react.md) | [Remix](https://bun.com/docs/guides/ecosystem/remix.md) | [Astro](https://bun.com/docs/guides/ecosystem/astro.md)
- [Nuxt](https://bun.com/docs/guides/ecosystem/nuxt.md) | [SvelteKit](https://bun.com/docs/guides/ecosystem/sveltekit.md) | [Vite](https://bun.com/docs/guides/ecosystem/vite.md) | [SolidStart](https://bun.com/docs/guides/ecosystem/solidstart.md)
- [Express](https://bun.com/docs/guides/ecosystem/express.md) | [Hono](https://bun.com/docs/guides/ecosystem/hono.md) | [Elysia](https://bun.com/docs/guides/ecosystem/elysia.md) | [Qwik](https://bun.com/docs/guides/ecosystem/qwik.md)

### Database Guides

- [Prisma](https://bun.com/docs/guides/ecosystem/prisma.md) | [Drizzle](https://bun.com/docs/guides/ecosystem/drizzle.md) | [MongoDB/Mongoose](https://bun.com/docs/guides/ecosystem/mongoose.md) | [Neon Postgres](https://bun.com/docs/guides/ecosystem/neon-serverless-postgres.md)

### Deployment

- [Docker](https://bun.com/docs/guides/ecosystem/docker.md) | [Vercel](https://bun.com/docs/guides/deployment/vercel.md) | [Railway](https://bun.com/docs/guides/deployment/railway.md) | [Render](https://bun.com/docs/guides/deployment/render.md)
- [AWS Lambda](https://bun.com/docs/guides/deployment/aws-lambda.md) | [Google Cloud Run](https://bun.com/docs/guides/deployment/google-cloud-run.md) | [DigitalOcean](https://bun.com/docs/guides/deployment/digital-ocean.md)
- [PM2](https://bun.com/docs/guides/ecosystem/pm2.md) | [systemd](https://bun.com/docs/guides/ecosystem/systemd.md)
- [GitHub Actions (runtime)](https://bun.com/docs/guides/runtime/cicd.md) | [GitHub Actions (install)](https://bun.com/docs/guides/install/cicd.md)

### Common Task Guides

- **HTTP**: [Simple server](https://bun.com/docs/guides/http/simple.md) | [Common usage](https://bun.com/docs/guides/http/server.md) | [fetch](https://bun.com/docs/guides/http/fetch.md) | [File uploads](https://bun.com/docs/guides/http/file-uploads.md) | [Streaming](https://bun.com/docs/guides/http/stream-file.md) | [Clustering](https://bun.com/docs/guides/http/cluster.md) | [TLS](https://bun.com/docs/guides/http/tls.md) | [Hot reload](https://bun.com/docs/guides/http/hot.md)
- **Files**: [Read string](https://bun.com/docs/guides/read-file/string.md) | [Read JSON](https://bun.com/docs/guides/read-file/json.md) | [Write](https://bun.com/docs/guides/write-file/basic.md) | [Append](https://bun.com/docs/guides/write-file/append.md) | [Copy](https://bun.com/docs/guides/write-file/file-cp.md) | [Delete](https://bun.com/docs/guides/write-file/unlink.md) | [Exists](https://bun.com/docs/guides/read-file/exists.md) | [Watch](https://bun.com/docs/guides/read-file/watch.md) | [MIME type](https://bun.com/docs/guides/read-file/mime.md)
- **WebSockets**: [Simple](https://bun.com/docs/guides/websocket/simple.md) | [Pub/sub](https://bun.com/docs/guides/websocket/pubsub.md) | [Compression](https://bun.com/docs/guides/websocket/compression.md)
- **Process**: [Spawn](https://bun.com/docs/guides/process/spawn.md) | [stdout/stderr](https://bun.com/docs/guides/process/spawn-stdout.md) | [IPC](https://bun.com/docs/guides/process/ipc.md) | [CLI args](https://bun.com/docs/guides/process/argv.md) | [CTRL+C](https://bun.com/docs/guides/process/ctrl-c.md) | [OS signals](https://bun.com/docs/guides/process/os-signals.md) | [Shell](https://bun.com/docs/guides/runtime/shell.md)
- **Utilities**: [Base64](https://bun.com/docs/guides/util/base64.md) | [Hash password](https://bun.com/docs/guides/util/hash-a-password.md) | [UUID](https://bun.com/docs/guides/util/javascript-uuid.md) | [Gzip](https://bun.com/docs/guides/util/gzip.md) | [Sleep](https://bun.com/docs/guides/util/sleep.md) | [Detect Bun](https://bun.com/docs/guides/util/detect-bun.md) | [Version](https://bun.com/docs/guides/util/version.md) | [Deep equals](https://bun.com/docs/guides/util/deep-equals.md) | [Escape HTML](https://bun.com/docs/guides/util/escape-html.md)
- **Testing**: [Run tests](https://bun.com/docs/guides/test/run-tests.md) | [Watch mode](https://bun.com/docs/guides/test/watch-mode.md) | [Mock functions](https://bun.com/docs/guides/test/mock-functions.md) | [Mock clock](https://bun.com/docs/guides/test/mock-clock.md) | [Snapshots](https://bun.com/docs/guides/test/snapshot.md) | [Coverage](https://bun.com/docs/guides/test/coverage.md) | [happy-dom](https://bun.com/docs/guides/test/happy-dom.md) | [Testing Library](https://bun.com/docs/guides/test/testing-library.md) | [Migrate from Jest](https://bun.com/docs/guides/test/migrate-from-jest.md)
- **Binary/Streams**: [Binary Data](https://bun.com/docs/runtime/binary-data.md) | [ArrayBuffer](https://bun.com/docs/guides/binary/arraybuffer-to-string.md) | [Blob](https://bun.com/docs/guides/binary/blob-to-string.md) | [Buffer](https://bun.com/docs/guides/binary/buffer-to-string.md) | [Stream](https://bun.com/docs/guides/streams/to-string.md)

### Templates & Scaffolding

- [bun create](https://bun.com/docs/runtime/templating/create.md) | [bun init](https://bun.com/docs/runtime/templating/init.md)

### Project Links

- [Roadmap](https://bun.com/docs/project/roadmap.md) | [Contributing](https://bun.com/docs/project/contributing.md) | [Feedback](https://bun.com/docs/feedback.md) | [Blog](https://bun.com/blog)
