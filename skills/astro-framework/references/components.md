# Astro Components Reference

## Component Script

The frontmatter (between `---`) runs on the server at build/request time. Never sent to client.

```astro
---
// Imports
import Header from './Header.astro';
import ReactComponent from './ReactComponent.jsx';
import data from '../data.json';

// Props with TypeScript
interface Props {
  title: string;
  description?: string;
  tags: string[];
}

const { title, description = 'Default', tags } = Astro.props;

// Async operations
const response = await fetch('https://api.example.com');
const apiData = await response.json();

// Access to global Astro object
const currentUrl = Astro.url;
const searchParams = Astro.url.searchParams;
const cookies = Astro.cookies;
const request = Astro.request;
---
```

## Template Expressions

```astro
---
const name = 'Astro';
const items = ['a', 'b', 'c'];
const show = true;
const htmlContent = '<strong>Bold</strong>';
---

<!-- Variables -->
<h1>{name}</h1>

<!-- Expressions -->
<p>{name.toUpperCase()}</p>

<!-- Conditionals -->
{show && <p>Visible</p>}
{show ? <p>Yes</p> : <p>No</p>}

<!-- Loops -->
<ul>
  {items.map(item => <li>{item}</li>)}
</ul>

<!-- Raw HTML (use carefully) -->
<div set:html={htmlContent} />

<!-- Dynamic attributes -->
<img src={url} alt={alt} {...props} />
```

## Slots

### Default Slot

```astro
<!-- Wrapper.astro -->
<div class="wrapper">
  <slot />
</div>

<!-- Usage -->
<Wrapper>
  <p>Content goes here</p>
</Wrapper>
```

### Named Slots

```astro
<!-- Layout.astro -->
<div>
  <header><slot name="header" /></header>
  <main><slot /></main>
  <footer><slot name="footer" /></footer>
</div>

<!-- Usage -->
<Layout>
  <h1 slot="header">Title</h1>
  <p>Main content (default slot)</p>
  <span slot="footer">Copyright</span>
</Layout>
```

### Fallback Content

```astro
<slot>
  <p>Default content if nothing passed</p>
</slot>
```

### Slot Checking

```astro
---
const hasHeader = Astro.slots.has('header');
---
{hasHeader && <header><slot name="header" /></header>}
```

### Render Slot to String

```astro
---
const content = await Astro.slots.render('default');
---
```

## Props Patterns

### TypeScript Interface

```astro
---
interface Props {
  title: string;
  count?: number;
  variant: 'primary' | 'secondary';
  onClick?: () => void;
}

const { title, count = 0, variant } = Astro.props;
---
```

### Passing Class

```astro
---
// MyComponent.astro
const { class: className, ...rest } = Astro.props;
---
<div class={className} {...rest}>
  <slot />
</div>
```

### Spread Props

```astro
---
const { href, ...attrs } = Astro.props;
---
<a href={href} {...attrs}><slot /></a>
```

## Styling

### Scoped Styles (Default)

```astro
<style>
  /* Only applies to this component */
  h1 { color: red; }
  .card { padding: 1rem; }
</style>
```

### Global Styles

```astro
<style is:global>
  /* Applies globally */
  body { margin: 0; }
</style>
```

### Mixed Scoped/Global

```astro
<style>
  h1 { color: red; }
  :global(.external-class) { color: blue; }
  .wrapper :global(p) { margin: 0; }
</style>
```

### CSS Variables from Props

```astro
---
const { color, size } = Astro.props;
---
<style define:vars={{ color, size }}>
  .box {
    color: var(--color);
    font-size: var(--size);
  }
</style>
<div class="box"><slot /></div>
```

### Import Stylesheets

```astro
---
import '../styles/global.css';
import styles from './Component.module.css';
---
<div class={styles.card}>...</div>
```

### class:list Directive

```astro
---
const { isActive, variant } = Astro.props;
---
<div class:list={[
  'base-class',
  { active: isActive },
  variant && `variant-${variant}`,
]}>
```

## Script Tags

### Bundled Scripts (Default)

```astro
<script>
  // Bundled, processed, runs once per page
  import { setup } from './utils';
  setup();
</script>
```

### Inline Scripts

```astro
<script is:inline>
  // Not bundled, runs as-is, may run multiple times
  console.log('Hello');
</script>
```

### Pass Data to Scripts

```astro
---
const data = { id: 1, name: 'Test' };
---
<script define:vars={{ data }}>
  console.log(data); // { id: 1, name: 'Test' }
</script>
```

### Re-run on Navigation (View Transitions)

```astro
<script>
  document.addEventListener('astro:page-load', () => {
    // Runs after each navigation
  });
</script>

<!-- Or force inline script to re-run -->
<script is:inline data-astro-rerun>
  console.log('Re-runs on each navigation');
</script>
```

## Special Elements

### Fragment

```astro
---
import { Fragment } from 'astro/jsx-runtime';
---
{items.map(item => (
  <Fragment>
    <dt>{item.term}</dt>
    <dd>{item.definition}</dd>
  </Fragment>
))}

<!-- Or use shorthand -->
{items.map(item => (
  <>
    <dt>{item.term}</dt>
    <dd>{item.definition}</dd>
  </>
))}
```

### Dynamic Tags

```astro
---
const { tag: Element = 'div' } = Astro.props;
---
<Element class="wrapper">
  <slot />
</Element>
```

## Directives

| Directive | Description |
|-----------|-------------|
| `set:html` | Inject raw HTML |
| `set:text` | Set text content |
| `is:global` | Make styles global |
| `is:inline` | Don't process script |
| `is:raw` | Don't process children |
| `define:vars` | Pass vars to style/script |
| `class:list` | Dynamic class names |
| `transition:*` | View transition directives |
| `client:*` | Framework hydration |
| `server:defer` | Server island |

## Framework Components

```astro
---
import ReactCounter from './Counter.jsx';
import VueCard from './Card.vue';
import SvelteForm from './Form.svelte';
---

<!-- No JS sent to client -->
<ReactCounter />

<!-- Hydrate on load -->
<ReactCounter client:load />

<!-- Hydrate when visible -->
<ReactCounter client:visible />

<!-- Hydrate when idle -->
<ReactCounter client:idle />

<!-- Hydrate on media query -->
<ReactCounter client:media="(max-width: 768px)" />

<!-- Only render on client -->
<ReactCounter client:only="react" />
```

## SVG Components

```astro
---
import Logo from '../assets/logo.svg';
---
<Logo width={64} height={64} fill="currentColor" />
```

## HTML Components

Import `.html` files as components:

```astro
---
import LegacyWidget from './widget.html';
---
<LegacyWidget />
```

Note: No frontmatter, scripts not bundled, only works with `public/` assets.
