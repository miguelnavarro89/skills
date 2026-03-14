# Content Collections Reference

## Setup

Create `src/content.config.ts` (or `.js`, `.mjs`):

```typescript
import { defineCollection } from 'astro:content';
import { glob, file } from 'astro/loaders';
import { z } from 'astro/zod';

// Define collections
const blog = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/blog" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    updatedDate: z.coerce.date().optional(),
    heroImage: z.string().optional(),
    draft: z.boolean().default(false),
    tags: z.array(z.string()).default([]),
  }),
});

const authors = defineCollection({
  loader: file("src/data/authors.json"),
  schema: z.object({
    id: z.string(),
    name: z.string(),
    email: z.string().email(),
    avatar: z.string().url(),
  }),
});

// Export all collections
export const collections = { blog, authors };
```

## Loaders

### glob() Loader

For directories of files (Markdown, MDX, JSON, YAML, TOML):

```typescript
import { glob } from 'astro/loaders';

const posts = defineCollection({
  loader: glob({ 
    pattern: "**/*.md",          // Glob pattern
    base: "./src/content/posts"   // Base directory
  }),
});

// Multiple patterns
const docs = defineCollection({
  loader: glob({ 
    pattern: ['*.md', '!draft-*'],  // Include .md, exclude draft-*
    base: 'src/content/docs'
  }),
});
```

### file() Loader

For single files with multiple entries:

```typescript
import { file } from 'astro/loaders';

const products = defineCollection({
  loader: file("src/data/products.json"),
});

// With custom parser (e.g., CSV)
import { parse as parseCsv } from "csv-parse/sync";

const data = defineCollection({
  loader: file("src/data/items.csv", {
    parser: (text) => parseCsv(text, { columns: true, skipEmptyLines: true })
  })
});

// Nested JSON
const dogs = defineCollection({
  loader: file("src/data/pets.json", {
    parser: (text) => JSON.parse(text).dogs
  })
});
```

### Custom/Remote Loaders

For external data sources:

```typescript
const countries = defineCollection({
  loader: async () => {
    const response = await fetch("https://api.example.com/countries");
    const data = await response.json();
    return data.map((item) => ({
      id: item.code,  // Required: unique ID
      ...item,
    }));
  },
});
```

## Schema Definition

### Common Types

```typescript
import { z } from 'astro/zod';

const schema = z.object({
  // Strings
  title: z.string(),
  slug: z.string().optional(),
  
  // Numbers
  order: z.number(),
  rating: z.number().min(1).max(5),
  
  // Booleans
  draft: z.boolean().default(false),
  featured: z.boolean().optional(),
  
  // Dates
  pubDate: z.coerce.date(),
  
  // Arrays
  tags: z.array(z.string()),
  
  // Enums
  status: z.enum(['draft', 'published', 'archived']),
  
  // Objects
  author: z.object({
    name: z.string(),
    email: z.string().email(),
  }),
  
  // Union types
  media: z.union([
    z.object({ type: z.literal('image'), url: z.string() }),
    z.object({ type: z.literal('video'), embedId: z.string() }),
  ]),
});
```

### Image Validation

```typescript
const blog = defineCollection({
  schema: ({ image }) => z.object({
    title: z.string(),
    cover: image(),  // Validates and imports local images
    coverAlt: z.string(),
  }),
});
```

### Collection References

```typescript
import { defineCollection, reference } from 'astro:content';

const blog = defineCollection({
  schema: z.object({
    title: z.string(),
    author: reference('authors'),           // Single reference
    relatedPosts: z.array(reference('blog')), // Array of references
  }),
});

const authors = defineCollection({
  schema: z.object({
    name: z.string(),
  }),
});
```

## Querying Collections

### Get All Entries

```astro
---
import { getCollection } from 'astro:content';

// Get all entries
const allPosts = await getCollection('blog');

// Filter entries
const publishedPosts = await getCollection('blog', ({ data }) => {
  return !data.draft;
});

// Filter by directory
const englishDocs = await getCollection('docs', ({ id }) => {
  return id.startsWith('en/');
});

// Sort entries
const sortedPosts = allPosts.sort(
  (a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf()
);
---
```

### Get Single Entry

```astro
---
import { getEntry } from 'astro:content';

const post = await getEntry('blog', 'my-post-slug');
if (!post) {
  return Astro.redirect('/404');
}
---
```

### Render Content

```astro
---
import { getEntry, render } from 'astro:content';

const post = await getEntry('blog', 'my-post');
const { Content, headings } = await render(post);
---

<article>
  <h1>{post.data.title}</h1>
  <Content />
</article>
```

### Resolve References

```astro
---
import { getEntry, getEntries } from 'astro:content';

const post = await getEntry('blog', 'welcome');

// Single reference
const author = await getEntry(post.data.author);

// Array of references
const relatedPosts = await getEntries(post.data.relatedPosts);
---

<p>By: {author.data.name}</p>
```

## Generating Pages

### Static Generation (SSG)

```astro
---
// src/pages/blog/[...slug].astro
import { getCollection, render } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);
---

<article>
  <h1>{post.data.title}</h1>
  <Content />
</article>
```

### Server-Side Rendering (SSR)

```astro
---
// src/pages/blog/[...slug].astro
export const prerender = false;

import { getEntry, render } from 'astro:content';

const { slug } = Astro.params;
const post = await getEntry('blog', slug);

if (!post) {
  return Astro.redirect('/404');
}

const { Content } = await render(post);
---

<article>
  <h1>{post.data.title}</h1>
  <Content />
</article>
```

## Custom IDs

Override auto-generated IDs in frontmatter:

```markdown
---
title: My Post
slug: custom-url-slug
---
```

Or in JSON:

```json
{
  "title": "My Item",
  "slug": "custom-id"
}
```

## TypeScript

### CollectionEntry Type

```typescript
import type { CollectionEntry } from 'astro:content';

interface Props {
  post: CollectionEntry<'blog'>;
}

const { post } = Astro.props;
// post.id, post.data.title, etc. are fully typed
```

### InferEntrySchema

```typescript
import type { InferEntrySchema } from 'astro:content';

type BlogData = InferEntrySchema<'blog'>;
```

## Best Practices

1. **Keep schemas strict** - Validate all expected properties
2. **Use defaults** - `z.default()` for optional fields with defaults
3. **Coerce dates** - `z.coerce.date()` handles various date formats
4. **Reference collections** - Use `reference()` for relationships
5. **Filter drafts** - Check `draft` field in production:

```typescript
const posts = await getCollection('blog', ({ data }) => {
  return import.meta.env.PROD ? !data.draft : true;
});
```

## JSON Schemas

Astro generates JSON schemas in `.astro/collections/` for IDE support:

```json
// In your JSON data file
{
  "$schema": "../../../.astro/collections/authors.schema.json",
  "name": "John",
  ...
}
```

VSCode settings for YAML:
```json
{
  "yaml.schemas": {
    "./.astro/collections/authors.schema.json": ["/src/data/authors/*.yml"]
  }
}
```
