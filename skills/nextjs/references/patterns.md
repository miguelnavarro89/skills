# Next.js Common Patterns

## Component Patterns

### Composition with Children

Pass Server Components through Client Components:

```tsx
// Client wrapper
'use client'
export function Modal({ children }: { children: React.ReactNode }) {
    const [open, setOpen] = useState(false)
    return open ? <div className="modal">{children}</div> : null
}

// Server parent
import { Modal } from './modal'
import { Cart } from './cart' // Server Component

export default function Page() {
    return (
        <Modal>
            <Cart /> {/* Rendered on server */}
        </Modal>
    )
}
```

### Context Providers

Wrap provider in Client Component:

```tsx
// providers/theme.tsx
'use client'
import { createContext, useContext } from 'react'

const ThemeContext = createContext('light')

export function ThemeProvider({ children }: { children: React.ReactNode }) {
    return (
        <ThemeContext.Provider value="dark">
            {children}
        </ThemeContext.Provider>
    )
}

export const useTheme = () => useContext(ThemeContext)
```

```tsx
// app/layout.tsx
import { ThemeProvider } from './providers/theme'

export default function RootLayout({ children }) {
    return (
        <html>
            <body>
                <ThemeProvider>{children}</ThemeProvider>
            </body>
        </html>
    )
}
```

### Third-Party Client Components

Wrap components without `'use client'`:

```tsx
// components/carousel.tsx
'use client'
export { Carousel } from 'acme-carousel'
```

## Data Patterns

### Request Deduplication

Use `React.cache` for non-fetch data:

```tsx
import { cache } from 'react'

export const getUser = cache(async (id: string) => {
    return db.user.findUnique({ where: { id } })
})
```

### Preloading Data

Start fetching before blocking operations:

```tsx
import { getItem } from '@/lib/data'

const preload = (id: string) => void getItem(id)

export default async function Page({ params }) {
    const { id } = await params
    
    // Start loading early
    preload(id)
    
    // Do other async work
    const isAvailable = await checkAvailability()
    
    return isAvailable ? <Item id={id} /> : null
}
```

### Sharing Data Between Components

Pass promise from server, resolve in client:

```tsx
// Server Component
export default function Layout({ children }) {
    const userPromise = getUser() // Don't await
    return (
        <UserProvider userPromise={userPromise}>
            {children}
        </UserProvider>
    )
}

// Client Component
'use client'
import { use, useContext } from 'react'

export function Profile() {
    const userPromise = useContext(UserContext)
    const user = use(userPromise)
    return <p>Welcome, {user.name}</p>
}
```

## Form Patterns

### Server Actions with Validation

```tsx
'use server'
import { z } from 'zod'
import { revalidatePath } from 'next/cache'

const schema = z.object({
    email: z.string().email(),
    name: z.string().min(2)
})

export async function createUser(formData: FormData) {
    const result = schema.safeParse({
        email: formData.get('email'),
        name: formData.get('name')
    })
    
    if (!result.success) {
        return { error: result.error.flatten() }
    }
    
    await db.user.create({ data: result.data })
    revalidatePath('/users')
    return { success: true }
}
```

### Optimistic Updates

```tsx
'use client'
import { useOptimistic, useTransition } from 'react'
import { addTodo } from './actions'

export function TodoList({ todos }) {
    const [optimisticTodos, addOptimisticTodo] = useOptimistic(
        todos,
        (state, newTodo) => [...state, { ...newTodo, pending: true }]
    )
    const [isPending, startTransition] = useTransition()
    
    async function formAction(formData: FormData) {
        const title = formData.get('title') as string
        addOptimisticTodo({ id: Date.now(), title })
        await addTodo(formData)
    }
    
    return (
        <form action={formAction}>
            <input name="title" />
            <ul>
                {optimisticTodos.map(todo => (
                    <li key={todo.id} style={{ opacity: todo.pending ? 0.5 : 1 }}>
                        {todo.title}
                    </li>
                ))}
            </ul>
        </form>
    )
}
```

## Loading Patterns

### Streaming with Suspense

```tsx
import { Suspense } from 'react'

export default function Dashboard() {
    return (
        <div>
            <h1>Dashboard</h1>
            
            {/* Shows immediately */}
            <StaticContent />
            
            {/* Streams in when ready */}
            <Suspense fallback={<ChartSkeleton />}>
                <SlowChart />
            </Suspense>
            
            <Suspense fallback={<TableSkeleton />}>
                <SlowTable />
            </Suspense>
        </div>
    )
}
```

### Sequential vs Parallel Data

```tsx
// Sequential (slower) - second waits for first
const user = await getUser(id)
const posts = await getPosts(user.id)

// Parallel (faster) - both start immediately
const userPromise = getUser(id)
const postsPromise = getPosts(id)
const [user, posts] = await Promise.all([userPromise, postsPromise])
```

## Security Patterns

### Server-Only Code

```tsx
import 'server-only'

export async function getSecretData() {
    const apiKey = process.env.SECRET_KEY
    return fetch(url, { headers: { Authorization: apiKey } })
}
```

### Validate User Input

Always validate on server:

```tsx
'use server'
import { z } from 'zod'

const DeleteSchema = z.object({
    id: z.string().uuid()
})

export async function deleteItem(formData: FormData) {
    const { id } = DeleteSchema.parse({
        id: formData.get('id')
    })
    
    // Verify ownership
    const item = await db.item.findUnique({ where: { id } })
    if (item.userId !== session.userId) {
        throw new Error('Unauthorized')
    }
    
    await db.item.delete({ where: { id } })
}
```

## Error Handling

### Error Boundaries

```tsx
// app/dashboard/error.tsx
'use client'

export default function Error({
    error,
    reset
}: {
    error: Error & { digest?: string }
    reset: () => void
}) {
    return (
        <div>
            <h2>Something went wrong!</h2>
            <button onClick={() => reset()}>Try again</button>
        </div>
    )
}
```

### Not Found

```tsx
// app/blog/[slug]/page.tsx
import { notFound } from 'next/navigation'

export default async function Page({ params }) {
    const { slug } = await params
    const post = await getPost(slug)
    
    if (!post) notFound()
    
    return <article>{post.content}</article>
}
```

```tsx
// app/blog/[slug]/not-found.tsx
export default function NotFound() {
    return <div>Post not found</div>
}
```

## Auth Patterns

### Protecting Routes

```tsx
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
    const token = request.cookies.get('token')
    
    if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
        return NextResponse.redirect(new URL('/login', request.url))
    }
}

export const config = {
    matcher: '/dashboard/:path*'
}
```

### Session in Server Components

```tsx
import { cookies } from 'next/headers'
import { redirect } from 'next/navigation'

export default async function DashboardPage() {
    const cookieStore = await cookies()
    const session = cookieStore.get('session')
    
    if (!session) redirect('/login')
    
    const user = await getUser(session.value)
    return <Dashboard user={user} />
}
```

## Reference

- [Data Security](https://nextjs.org/docs/app/guides/data-security)
- [Authentication Guide](https://nextjs.org/docs/app/guides/authentication)
- [Forms Guide](https://nextjs.org/docs/app/guides/forms)
