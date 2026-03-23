---
name: manr:nextjs
description: Build Next.js 16 applications with App Router, Server/Client Components, data fetching, caching, and routing. Use when creating Next.js projects, pages, layouts, route handlers, server actions, or when asking about App Router patterns, RSC, streaming, or caching strategies.
---

# Next.js (App Router)

## Quick Start

```bash
# Create new project
npx create-next-app@latest my-app --yes
cd my-app && npm run dev
```

Default setup: TypeScript, Tailwind, ESLint, App Router, Turbopack.

## Core Concepts

### File-System Routing

Routes are defined by folder structure in `app/`:

| File | Purpose |
|------|---------|
| `page.tsx` | Page UI (makes route public) |
| `layout.tsx` | Shared UI wrapper |
| `loading.tsx` | Loading skeleton (Suspense) |
| `error.tsx` | Error boundary |
| `not-found.tsx` | 404 UI |
| `route.ts` | API endpoint |

### Server vs Client Components

**Server Components** (default):
- Fetch data, access DB/secrets
- Reduce JS bundle
- No state/effects/browser APIs

**Client Components** (`'use client'`):
- State, effects, event handlers
- Browser APIs
- Interactivity

```tsx
// Server Component (default)
export default async function Page() {
    const data = await fetch('https://api.example.com/data')
    return <ClientButton data={data} />
}

// Client Component
'use client'
import { useState } from 'react'
export function ClientButton({ data }) {
    const [count, setCount] = useState(0)
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

### Dynamic Routes

| Pattern | Example URL | Usage |
|---------|-------------|-------|
| `[slug]` | `/blog/hello` | Single param |
| `[...slug]` | `/docs/a/b/c` | Catch-all |
| `[[...slug]]` | `/docs` or `/docs/a` | Optional catch-all |

```tsx
// app/blog/[slug]/page.tsx
export default async function Page({
    params
}: { params: Promise<{ slug: string }> }) {
    const { slug } = await params
    return <h1>{slug}</h1>
}
```

### Route Groups & Private Folders

- `(group)` - Organize without affecting URL
- `_folder` - Private, not routable

```
app/
├── (marketing)/
│   └── page.tsx         → /
├── (dashboard)/
│   └── settings/
│       └── page.tsx     → /settings
└── _components/         → Not routable
```

## Data Fetching

### Server Components

```tsx
// Direct fetch
export default async function Page() {
    const data = await fetch('https://api.example.com/data')
    return <div>{data.title}</div>
}

// With database
import { db } from '@/lib/db'
export default async function Page() {
    const posts = await db.select().from(posts)
    return <PostList posts={posts} />
}
```

### Client Components

```tsx
'use client'
import { use } from 'react'

// Via promise from server
export function Posts({ posts }: { posts: Promise<Post[]> }) {
    const data = use(posts)
    return <ul>{data.map(p => <li key={p.id}>{p.title}</li>)}</ul>
}
```

### Parallel Fetching

```tsx
export default async function Page() {
    // Start both requests simultaneously
    const artistData = getArtist(id)
    const albumsData = getAlbums(id)
    
    const [artist, albums] = await Promise.all([artistData, albumsData])
    return <div>{artist.name}</div>
}
```

## Caching & Revalidation

### fetch Options

```tsx
// Cache indefinitely
fetch(url, { cache: 'force-cache' })

// Revalidate every hour
fetch(url, { next: { revalidate: 3600 } })

// Tag for on-demand revalidation
fetch(url, { next: { tags: ['posts'] } })
```

### use cache Directive

```tsx
import { cacheTag, cacheLife } from 'next/cache'

export async function getProducts() {
    'use cache'
    cacheTag('products')
    cacheLife('hours')
    return db.query('SELECT * FROM products')
}
```

### Revalidation

```tsx
'use server'
import { revalidateTag, revalidatePath, updateTag } from 'next/cache'

export async function updateProduct() {
    await db.products.update(...)
    
    // Options:
    revalidateTag('products', 'max')  // Stale-while-revalidate
    updateTag('products')              // Immediate (Server Actions only)
    revalidatePath('/products')        // By path
}
```

## Server Actions

```tsx
// app/actions.ts
'use server'

export async function createPost(formData: FormData) {
    const title = formData.get('title')
    await db.post.create({ data: { title } })
    revalidateTag('posts')
}

// Usage in component
import { createPost } from './actions'

export function Form() {
    return (
        <form action={createPost}>
            <input name="title" />
            <button type="submit">Create</button>
        </form>
    )
}
```

## Route Handlers (API)

```tsx
// app/api/posts/route.ts
import { NextRequest } from 'next/server'

export async function GET(request: NextRequest) {
    const searchParams = request.nextUrl.searchParams
    const query = searchParams.get('q')
    return Response.json({ data: [] })
}

export async function POST(request: Request) {
    const body = await request.json()
    return Response.json({ created: true }, { status: 201 })
}

// Dynamic route: app/api/posts/[id]/route.ts
export async function GET(
    request: Request,
    { params }: { params: Promise<{ id: string }> }
) {
    const { id } = await params
    return Response.json({ id })
}
```

## Streaming & Suspense

```tsx
import { Suspense } from 'react'

export default function Page() {
    return (
        <div>
            <h1>Dashboard</h1>
            <Suspense fallback={<Skeleton />}>
                <SlowComponent />
            </Suspense>
        </div>
    )
}
```

Or use `loading.tsx` for entire page streaming.

## Components

### Link

```tsx
import Link from 'next/link'

<Link href="/dashboard">Dashboard</Link>
<Link href="/dashboard" replace>Replace history</Link>
<Link href="/dashboard" scroll={false}>Keep scroll</Link>
<Link href="/dashboard" prefetch={false}>No prefetch</Link>
```

### Image

```tsx
import Image from 'next/image'

// Local image (auto width/height)
import profilePic from './profile.png'
<Image src={profilePic} alt="Profile" />

// Remote image (explicit dimensions)
<Image
    src="https://example.com/photo.jpg"
    alt="Photo"
    width={500}
    height={300}
/>

// Fill container
<div style={{ position: 'relative', width: '100%', height: 300 }}>
    <Image src="/bg.jpg" alt="Background" fill style={{ objectFit: 'cover' }} />
</div>
```

Configure remote patterns in `next.config.js`:

```js
module.exports = {
    images: {
        remotePatterns: [
            new URL('https://example.com/**')
        ]
    }
}
```

## Metadata

```tsx
// Static
export const metadata = {
    title: 'My Page',
    description: 'Page description'
}

// Dynamic
export async function generateMetadata({ params }) {
    const { id } = await params
    const product = await getProduct(id)
    return { title: product.name }
}
```

## Additional Resources

- [Project structure](references/project-structure.md) - File conventions and organization
- [patterns.md](references/patterns.md) - Common patterns and best practices

## Reference Links

- [Installation](https://nextjs.org/docs/app/getting-started/installation)
- [Project Structure](https://nextjs.org/docs/app/getting-started/project-structure)
- [Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages)
- [Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [Fetching Data](https://nextjs.org/docs/app/getting-started/fetching-data)
- [Caching and Revalidating](https://nextjs.org/docs/app/getting-started/caching-and-revalidating)
- [use server directive](https://nextjs.org/docs/app/api-reference/directives/use-server)
- [use cache directive](https://nextjs.org/docs/app/api-reference/directives/use-cache)
- [Image Component](https://nextjs.org/docs/app/api-reference/components/image)
- [Link Component](https://nextjs.org/docs/app/api-reference/components/link)
- [Route Handlers](https://nextjs.org/docs/app/api-reference/file-conventions/route)
