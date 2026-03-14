# Next.js Project Structure

## Top-Level Folders

| Folder | Purpose |
|--------|---------|
| `app` | App Router |
| `public` | Static assets |
| `src` | Optional source folder |

## Top-Level Files

| File | Purpose |
|------|---------|
| `next.config.js` | Next.js configuration |
| `package.json` | Dependencies and scripts |
| `instrumentation.ts` | OpenTelemetry instrumentation |
| `proxy.ts` | Request proxy |
| `.env*` | Environment variables |
| `eslint.config.mjs` | ESLint configuration |
| `tsconfig.json` | TypeScript configuration |

## Routing Files

| File | Extension | Purpose |
|------|-----------|---------|
| `layout` | `.tsx` | Shared UI wrapper |
| `page` | `.tsx` | Page UI (makes route public) |
| `loading` | `.tsx` | Loading UI (Suspense boundary) |
| `not-found` | `.tsx` | Not found UI |
| `error` | `.tsx` | Error UI (Error boundary) |
| `global-error` | `.tsx` | Global error UI |
| `route` | `.ts` | API endpoint |
| `template` | `.tsx` | Re-rendered layout |
| `default` | `.tsx` | Parallel route fallback |

## Dynamic Routes

| Syntax | URL Pattern | Access |
|--------|-------------|--------|
| `[slug]` | `/blog/my-post` | `params.slug` |
| `[...slug]` | `/docs/a/b/c` | `params.slug = ['a','b','c']` |
| `[[...slug]]` | `/docs` or `/docs/a/b` | Optional |

## Route Groups & Private Folders

```
app/
├── (marketing)/              # Group - URL: /
│   ├── layout.tsx           # Shared marketing layout
│   └── page.tsx
├── (shop)/                   # Group - URL: /cart, /checkout
│   ├── layout.tsx           # Shared shop layout
│   ├── cart/
│   │   └── page.tsx
│   └── checkout/
│       └── page.tsx
├── _components/              # Private - not routable
│   └── Button.tsx
└── _lib/                     # Private - not routable
    └── utils.ts
```

## Parallel Routes

Named slots rendered by parent layout:

```
app/
├── layout.tsx               # Renders @analytics and children
├── @analytics/
│   └── page.tsx
└── page.tsx
```

```tsx
// app/layout.tsx
export default function Layout({
    children,
    analytics
}: {
    children: React.ReactNode
    analytics: React.ReactNode
}) {
    return (
        <div>
            {children}
            {analytics}
        </div>
    )
}
```

## Intercepting Routes

| Pattern | Meaning |
|---------|---------|
| `(.)folder` | Same level |
| `(..)folder` | One level up |
| `(..)(..)folder` | Two levels up |
| `(...)folder` | From root |

Example: Photo modal over feed

```
app/
├── feed/
│   └── page.tsx             # Feed page
├── photo/
│   └── [id]/
│       └── page.tsx         # Full photo page
└── @modal/
    └── (.)photo/
        └── [id]/
            └── page.tsx     # Intercepted modal
```

## Metadata Files

### App Icons
| File | Format | Purpose |
|------|--------|---------|
| `favicon` | `.ico` | Browser favicon |
| `icon` | `.ico/.png/.svg` | App icon |
| `apple-icon` | `.png` | Apple app icon |

### Open Graph
| File | Format | Purpose |
|------|--------|---------|
| `opengraph-image` | `.jpg/.png` | OG image |
| `twitter-image` | `.jpg/.png` | Twitter card |

### SEO
| File | Format | Purpose |
|------|--------|---------|
| `sitemap` | `.xml` or `.ts` | Site map |
| `robots` | `.txt` or `.ts` | Robots file |

## Component Hierarchy

Components render in this order (outer to inner):

1. `layout.tsx`
2. `template.tsx`
3. `error.tsx` (boundary)
4. `loading.tsx` (suspense)
5. `not-found.tsx` (boundary)
6. `page.tsx` or nested `layout.tsx`

## Organization Strategies

### 1. Store files outside `app`

```
project/
├── app/                     # Only routing
│   └── page.tsx
├── components/              # Shared components
├── lib/                     # Utilities
└── hooks/                   # Custom hooks
```

### 2. Store files inside `app`

```
app/
├── _components/             # Shared components
├── _lib/                    # Utilities
├── dashboard/
│   └── page.tsx
└── page.tsx
```

### 3. Feature-based colocation

```
app/
├── dashboard/
│   ├── _components/         # Dashboard-specific
│   ├── _lib/
│   └── page.tsx
└── page.tsx
```

## Reference

- [Project Structure Docs](https://nextjs.org/docs/app/getting-started/project-structure)
- [Route Groups](https://nextjs.org/docs/app/api-reference/file-conventions/route-groups)
- [Parallel Routes](https://nextjs.org/docs/app/api-reference/file-conventions/parallel-routes)
- [Intercepting Routes](https://nextjs.org/docs/app/api-reference/file-conventions/intercepting-routes)
