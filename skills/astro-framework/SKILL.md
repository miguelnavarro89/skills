---
name: astro-framework
description: Build Astro websites using components, pages, layouts, content collections, routing, SSR, View Transitions, and integrations. Use when creating Astro projects, .astro files, content collections, server islands, actions, or when asking about Astro patterns, Islands Architecture, or static site generation.
---

# Astro Framework

Astro is a web framework for building content-driven websites. Ships zero JavaScript by default using Islands Architecture.

## Quick Start

```bash
# Create new project (choose your package manager)
npm create astro@latest
pnpm create astro@latest
bun create astro@latest

# Add integrations
npx astro add react tailwind
bunx astro add react tailwind
```

## Package Manager Commands

| Task | npm | bun |
|------|-----|-----|
| Create project | `npm create astro@latest` | `bun create astro@latest` |
| Dev server | `npm run dev` | `bun dev` |
| Build | `npm run build` | `bun run build` |
| Preview | `npm run preview` | `bun preview` |
| Add integration | `npx astro add <name>` | `bunx astro add <name>` |
| Check types | `npx astro check` | `bunx astro check` |

## Project Structure

```
src/
├── pages/          # File-based routing (REQUIRED)
├── components/     # Reusable .astro components
├── layouts/        # Page templates with <slot/>
├── content/        # Content collections
├── styles/         # CSS/SCSS files
├── assets/         # Optimized images
public/             # Static assets (unprocessed)
astro.config.mjs    # Configuration
content.config.ts   # Collection schemas
```

## Component Anatomy

```astro
---
// Component Script (runs on server, never sent to client)
import Header from './Header.astro';
import { getCollection } from 'astro:content';

interface Props {
  title: string;
  count?: number;
}

const { title, count = 0 } = Astro.props;
const posts = await getCollection('blog');
---

<!-- Component Template -->
<Header />
<h1>{title}</h1>
<ul>
  {posts.map(post => <li>{post.data.title}</li>)}
</ul>

<style>
  /* Scoped by default */
  h1 { color: blue; }
</style>

<style is:global>
  /* Global styles */
</style>
```

## Pages & Routing

File-based routing from `src/pages/`:

| File | Route |
|------|-------|
| `index.astro` | `/` |
| `about.astro` | `/about` |
| `blog/[slug].astro` | `/blog/:slug` |
| `[...path].astro` | catch-all |

### Dynamic Routes (SSG)

```astro
---
// src/pages/posts/[slug].astro
export function getStaticPaths() {
  return [
    { params: { slug: 'post-1' }, props: { title: 'First' } },
    { params: { slug: 'post-2' }, props: { title: 'Second' } },
  ];
}

const { slug } = Astro.params;
const { title } = Astro.props;
---
<h1>{title}</h1>
```

## Layouts

```astro
---
// src/layouts/BaseLayout.astro
interface Props {
  title: string;
}
const { title } = Astro.props;
---
<html lang="en">
  <head>
    <title>{title}</title>
  </head>
  <body>
    <slot /> <!-- Child content injected here -->
  </body>
</html>
```

Usage:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---
<BaseLayout title="Home">
  <h1>Welcome</h1>
</BaseLayout>
```

## Content Collections

Define in `src/content.config.ts`:

```typescript
import { defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';
import { z } from 'astro/zod';

const blog = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/blog" }),
  schema: z.object({
    title: z.string(),
    pubDate: z.coerce.date(),
    draft: z.boolean().optional(),
  }),
});

export const collections = { blog };
```

Query collections:
```astro
---
import { getCollection, getEntry, render } from 'astro:content';

const posts = await getCollection('blog', ({ data }) => !data.draft);
const post = await getEntry('blog', 'my-post');
const { Content } = await render(post);
---
<Content />
```

## UI Framework Islands

Add framework support:
```bash
# npm
npx astro add react    # React 19 support
npx astro add vue      # Vue 3
npx astro add svelte   # Svelte 5
npx astro add solid    # SolidJS
npx astro add preact   # Preact

# bun
bunx astro add react vue svelte  # Can add multiple at once
```

Use `client:*` directives for hydration:

```astro
---
import Counter from './Counter.jsx';
---
<!-- Static by default (no JS) -->
<Counter />

<!-- Hydrated on page load -->
<Counter client:load />

<!-- Hydrated when visible -->
<Counter client:visible />

<!-- Hydrated on idle -->
<Counter client:idle />

<!-- Hydrated on media query -->
<Counter client:media="(max-width: 768px)" />
```

## Server-Side Rendering (SSR)

Enable on-demand rendering:

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import node from '@astrojs/node';

export default defineConfig({
  output: 'server', // or 'static' (default)
  adapter: node({ mode: 'standalone' }),
});
```

Per-page opt-in/out:
```astro
---
export const prerender = false; // Render on-demand
// export const prerender = true; // Pre-render at build
---
```

Access request data:
```astro
---
const cookie = Astro.cookies.get('session');
const url = Astro.url;
const headers = Astro.request.headers;
---
```

## Server Islands

Defer component rendering until content is ready:

```astro
---
import Avatar from './Avatar.astro';
---
<Avatar server:defer>
  <div slot="fallback">Loading...</div>
</Avatar>
```

## Actions

Type-safe backend functions in `src/actions/index.ts`:

```typescript
import { defineAction, ActionError } from 'astro:actions';
import { z } from 'astro/zod';

export const server = {
  subscribe: defineAction({
    accept: 'form',
    input: z.object({
      email: z.string().email(),
    }),
    handler: async ({ email }, ctx) => {
      if (!ctx.cookies.has('session')) {
        throw new ActionError({ code: 'UNAUTHORIZED' });
      }
      // Process subscription
      return { success: true };
    },
  }),
};
```

Call from client:
```astro
<script>
import { actions } from 'astro:actions';

const { data, error } = await actions.subscribe({ email: 'test@example.com' });
</script>
```

## View Transitions

Enable SPA-like navigation:

```astro
---
import { ClientRouter } from 'astro:transitions';
---
<head>
  <ClientRouter />
</head>

<!-- Animate elements -->
<div transition:animate="slide">
<img transition:name="hero" transition:persist />
```

## Images

```astro
---
import { Image, Picture } from 'astro:assets';
import myImage from '../assets/hero.png';
---
<Image src={myImage} alt="Hero" />
<Image src="/public-image.jpg" alt="Public" width={800} height={600} />
<Picture src={myImage} formats={['avif', 'webp']} alt="Hero" />
```

## Styling

```astro
<style>
  /* Scoped to component */
  h1 { color: red; }
</style>

<style is:global>
  /* Global styles */
</style>

<style define:vars={{ color: 'blue' }}>
  h1 { color: var(--color); }
</style>
```

### Tailwind CSS v4

```bash
npx astro add tailwind
bunx astro add tailwind
```

### MDX Support

```bash
npx astro add mdx
bunx astro add mdx
```

Then use `.mdx` files in content collections or pages:

```mdx
---
title: My Post
---
import MyComponent from '../components/MyComponent.astro';

# Hello {props.title}

<MyComponent client:load />
```

## Configuration

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://example.com',
  base: '/docs',
  output: 'static', // 'static' | 'server'
  trailingSlash: 'always', // 'always' | 'never' | 'ignore'
  integrations: [],
  vite: {},
});
```

## Common Patterns

### API Endpoints

```typescript
// src/pages/api/users.ts
import type { APIRoute } from 'astro';

export const GET: APIRoute = async ({ params, request }) => {
  return new Response(JSON.stringify({ users: [] }), {
    headers: { 'Content-Type': 'application/json' },
  });
};

export const POST: APIRoute = async ({ request }) => {
  const data = await request.json();
  return new Response(JSON.stringify({ success: true }));
};
```

### Data Fetching

```astro
---
const response = await fetch('https://api.example.com/data');
const data = await response.json();
---
```

### Error Pages

Create `src/pages/404.astro` and `src/pages/500.astro`.

### Redirects

```javascript
// astro.config.mjs
export default defineConfig({
  redirects: {
    '/old': '/new',
    '/blog/[...slug]': '/articles/[...slug]',
  },
});
```

## Common Integrations

```bash
# All-in-one: React + Tailwind + MDX
bunx astro add react tailwind mdx

# SSR adapters
bunx astro add node       # Node.js standalone
bunx astro add vercel     # Vercel
bunx astro add netlify    # Netlify
bunx astro add cloudflare # Cloudflare Workers
```

## Key References

- [Getting Started](https://docs.astro.build/en/getting-started/) - Installation, setup, concepts
- [Islands Architecture](https://docs.astro.build/en/concepts/islands/) - Core design principles
- [Framework Components](https://docs.astro.build/en/guides/framework-components/) - React/Vue/Svelte integration
- [Content Collections](https://docs.astro.build/en/guides/content-collections/) - Type-safe content management
- [View Transitions](https://docs.astro.build/en/guides/view-transitions/) - SPA-like navigation
- [On-Demand Rendering](https://docs.astro.build/en/guides/on-demand-rendering/) - SSR patterns
- [Configuration Reference](https://docs.astro.build/en/reference/configuration-reference/) - Full config options
- [API Reference](https://docs.astro.build/en/reference/api-reference/) - Astro global object
- [Directives Reference](https://docs.astro.build/en/reference/directives-reference/) - Client/server directives

## Additional Resources

- [components.md](references/components.md) - Detailed component patterns
- [collections.md](references/collections.md) - Content collections deep dive
- [ssr.md](references/ssr.md) - Server-side rendering patterns
