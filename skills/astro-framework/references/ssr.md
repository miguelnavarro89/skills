# Server-Side Rendering Reference

## Rendering Modes

### Static (Default)

All pages pre-rendered at build time:

```javascript
// astro.config.mjs
export default defineConfig({
  output: 'static', // Default
});
```

### Server (On-Demand)

All pages rendered on request:

```javascript
// astro.config.mjs
import node from '@astrojs/node';

export default defineConfig({
  output: 'server',
  adapter: node({ mode: 'standalone' }),
});
```

### Hybrid

Mix static and on-demand pages:

```javascript
// astro.config.mjs
export default defineConfig({
  output: 'static', // Default to static
  adapter: node({ mode: 'standalone' }),
});
```

Per-page opt-in/out:

```astro
---
// In a 'static' project, opt into server rendering
export const prerender = false;
---
```

```astro
---
// In a 'server' project, opt into static rendering
export const prerender = true;
---
```

## Adapters

### Official Adapters

```bash
# npm
npx astro add node       # Node.js
npx astro add vercel     # Vercel
npx astro add netlify    # Netlify
npx astro add cloudflare # Cloudflare

# bun
bunx astro add node
bunx astro add vercel
```

### Manual Setup

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import node from '@astrojs/node';

export default defineConfig({
  output: 'server',
  adapter: node({
    mode: 'standalone', // or 'middleware'
  }),
});
```

## Request & Response

### Access Request Data

```astro
---
export const prerender = false;

// URL info
const url = Astro.url;
const pathname = Astro.url.pathname;
const searchParams = Astro.url.searchParams;
const query = searchParams.get('q');

// Request object
const method = Astro.request.method;
const headers = Astro.request.headers;
const userAgent = headers.get('User-Agent');
const body = await Astro.request.json(); // For POST

// Client IP (adapter-dependent)
const ip = Astro.clientAddress;

// Locals (from middleware)
const user = Astro.locals.user;
---
```

### Set Response

```astro
---
// Set status
Astro.response.status = 404;
Astro.response.statusText = 'Not Found';

// Set headers
Astro.response.headers.set('Cache-Control', 'public, max-age=3600');
Astro.response.headers.set('X-Custom-Header', 'value');
---
```

### Return Response Object

```astro
---
import { getProduct } from '../api';

const product = await getProduct(Astro.params.id);

if (!product) {
  return new Response(null, {
    status: 404,
    statusText: 'Not Found',
  });
}

if (!product.available) {
  return Astro.redirect('/products', 301);
}
---
<h1>{product.name}</h1>
```

## Cookies

```astro
---
// Get cookie
const sessionCookie = Astro.cookies.get('session');
const sessionValue = sessionCookie?.value;
const sessionNumber = sessionCookie?.number();
const sessionBoolean = sessionCookie?.boolean();
const sessionJson = sessionCookie?.json();

// Check if exists
if (Astro.cookies.has('session')) {
  // ...
}

// Set cookie
Astro.cookies.set('theme', 'dark', {
  path: '/',
  maxAge: 60 * 60 * 24 * 7, // 1 week
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
});

// Delete cookie
Astro.cookies.delete('session');
---
```

## Redirects

### Programmatic

```astro
---
if (!isLoggedIn) {
  return Astro.redirect('/login');
}

// With status code
return Astro.redirect('/new-page', 301);
return Astro.redirect('/temporary', 302);
---
```

### Configuration

```javascript
// astro.config.mjs
export default defineConfig({
  redirects: {
    '/old': '/new',
    '/old/[...slug]': '/new/[...slug]',
    '/external': 'https://example.com',
    '/with-status': {
      status: 302,
      destination: '/target',
    },
  },
});
```

## API Endpoints

### GET Endpoint

```typescript
// src/pages/api/users.ts
import type { APIRoute } from 'astro';

export const GET: APIRoute = async ({ params, request, cookies, locals }) => {
  const users = await getUsers();
  
  return new Response(JSON.stringify(users), {
    status: 200,
    headers: {
      'Content-Type': 'application/json',
    },
  });
};
```

### POST Endpoint

```typescript
// src/pages/api/contact.ts
import type { APIRoute } from 'astro';

export const POST: APIRoute = async ({ request }) => {
  const data = await request.json();
  // or: const data = await request.formData();
  
  // Process data...
  
  return new Response(JSON.stringify({ success: true }), {
    status: 201,
    headers: { 'Content-Type': 'application/json' },
  });
};
```

### All Methods

```typescript
// src/pages/api/resource/[id].ts
import type { APIRoute } from 'astro';

export const GET: APIRoute = async ({ params }) => {
  const { id } = params;
  // ...
};

export const PUT: APIRoute = async ({ params, request }) => {
  // ...
};

export const DELETE: APIRoute = async ({ params }) => {
  // ...
};

// Catch-all for other methods
export const ALL: APIRoute = async () => {
  return new Response('Method not allowed', { status: 405 });
};
```

### Static vs Dynamic Endpoints

```typescript
// Static endpoint (generated at build)
export const prerender = true;

export function getStaticPaths() {
  return [
    { params: { id: '1' } },
    { params: { id: '2' } },
  ];
}

export const GET: APIRoute = ({ params }) => {
  // ...
};
```

## Middleware

Create `src/middleware.ts`:

```typescript
import { defineMiddleware, sequence } from 'astro:middleware';

// Single middleware
export const onRequest = defineMiddleware(async (context, next) => {
  const { request, cookies, locals, redirect } = context;
  
  // Before route handler
  const session = cookies.get('session');
  if (!session && context.url.pathname.startsWith('/admin')) {
    return redirect('/login');
  }
  
  // Set locals (available in pages via Astro.locals)
  locals.user = await getUserFromSession(session);
  
  // Continue to route
  const response = await next();
  
  // After route handler (modify response)
  response.headers.set('X-Custom-Header', 'value');
  
  return response;
});
```

### Chained Middleware

```typescript
import { sequence } from 'astro:middleware';

const auth = defineMiddleware(async (context, next) => {
  // Auth logic
  return next();
});

const logging = defineMiddleware(async (context, next) => {
  console.log(`${context.request.method} ${context.url.pathname}`);
  return next();
});

export const onRequest = sequence(auth, logging);
```

### Type Locals

```typescript
// src/env.d.ts
declare namespace App {
  interface Locals {
    user: {
      id: string;
      name: string;
    } | null;
  }
}
```

## Server Islands

Defer rendering of specific components:

```astro
---
import UserAvatar from './UserAvatar.astro';
---
<UserAvatar server:defer>
  <div slot="fallback">
    <div class="skeleton-avatar"></div>
  </div>
</UserAvatar>
```

The component renders after page load, fetched independently.

## HTML Streaming

Astro streams HTML to the browser as components render. For long-running operations:

```astro
---
// These run in parallel
const [users, posts] = await Promise.all([
  fetchUsers(),
  fetchPosts(),
]);
---
```

Note: Response headers must be set before streaming starts.

## Environment Variables

```typescript
// Server-side (available in .astro files)
const apiKey = import.meta.env.API_KEY;

// Public (also available client-side)
const publicUrl = import.meta.env.PUBLIC_API_URL;
```

With typed environment variables:

```typescript
// astro.config.mjs
import { defineConfig, envField } from 'astro/config';

export default defineConfig({
  env: {
    schema: {
      API_KEY: envField.string({ context: 'server', access: 'secret' }),
      PUBLIC_API_URL: envField.string({ context: 'client', access: 'public' }),
    },
  },
});
```

```typescript
// Usage
import { API_KEY, PUBLIC_API_URL } from 'astro:env/server';
import { PUBLIC_API_URL } from 'astro:env/client';
```

## Sessions

```typescript
// astro.config.mjs
export default defineConfig({
  adapter: node({ mode: 'standalone' }),
  session: {
    driver: 'redis',
    options: {
      url: process.env.REDIS_URL,
    },
    ttl: 3600, // 1 hour
  },
});
```

```astro
---
// Get session
const session = await Astro.session.get('cart');

// Set session
await Astro.session.set('cart', { items: [] });

// Delete session
await Astro.session.delete('cart');

// Destroy all session data
await Astro.session.destroy();
---
```

## Security

### CSRF Protection

Enabled by default. Validates origin header for POST/PUT/DELETE:

```javascript
export default defineConfig({
  security: {
    checkOrigin: true, // Default
  },
});
```

### Trusted Hosts

```javascript
export default defineConfig({
  security: {
    allowedDomains: [
      { hostname: '*.example.com', protocol: 'https' },
    ],
  },
});
```
