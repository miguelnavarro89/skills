---
name: tailwindcss
description: Install and configure Tailwind CSS v4 for Vite, PostCSS, or Next.js projects. Customize themes with @theme directive, set up dark mode, extend design tokens. Use when setting up Tailwind, configuring themes, adding custom colors/fonts/spacing, or when user mentions Tailwind installation, @theme, CSS variables, or design tokens.
---

# Tailwind CSS v4

## Installation by Framework

### Next.js (PostCSS)

```bash
npm install tailwindcss @tailwindcss/postcss postcss
```

**postcss.config.mjs**:
```js
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

**app/globals.css**:
```css
@import "tailwindcss";
```

### Vite (React, SvelteKit, SolidJS, etc.)

```bash
npm install tailwindcss @tailwindcss/vite
```

**vite.config.ts**:
```ts
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [tailwindcss()],
})
```

**src/style.css**:
```css
@import "tailwindcss";
```

### Generic PostCSS

```bash
npm install tailwindcss @tailwindcss/postcss postcss
```

**postcss.config.mjs**:
```js
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

---

## Theme Customization with @theme

Theme variables create utility classes automatically. Define in your main CSS file after `@import "tailwindcss"`.

### Extend Default Theme

```css
@import "tailwindcss";

@theme {
  --color-brand: oklch(0.72 0.11 221);
  --color-brand-light: oklch(0.85 0.08 221);
  --font-display: "Cal Sans", sans-serif;
  --breakpoint-3xl: 120rem;
}
```

Creates utilities: `bg-brand`, `text-brand-light`, `font-display`, `3xl:*`

### Override Namespace

Reset entire namespace with `--namespace-*: initial`:

```css
@theme {
  --color-*: initial;
  --color-white: #fff;
  --color-black: #000;
  --color-primary: oklch(0.62 0.21 255);
  --color-secondary: oklch(0.70 0.14 182);
}
```

### Full Custom Theme

```css
@theme {
  --*: initial;
  --spacing: 4px;
  --font-body: Inter, sans-serif;
  --color-surface: oklch(0.98 0 0);
  --color-text: oklch(0.15 0 0);
}
```

---

## Theme Variable Namespaces

| Namespace | Creates |
|-----------|---------|
| `--color-*` | `bg-*`, `text-*`, `border-*`, `fill-*`, `stroke-*` |
| `--font-*` | `font-*` (font-family) |
| `--text-*` | `text-*` (font-size) |
| `--font-weight-*` | `font-*` (weight) |
| `--spacing-*` | `p-*`, `m-*`, `gap-*`, `w-*`, `h-*` |
| `--radius-*` | `rounded-*` |
| `--shadow-*` | `shadow-*` |
| `--breakpoint-*` | `sm:`, `md:`, `lg:` variants |
| `--animate-*` | `animate-*` |
| `--ease-*` | `ease-*` |

---

## Dark Mode

### CSS-based (class strategy)

```css
@import "tailwindcss";

@theme {
  --color-bg: oklch(0.98 0 0);
  --color-text: oklch(0.15 0 0);
}

.dark {
  --color-bg: oklch(0.15 0 0);
  --color-text: oklch(0.95 0 0);
}
```

Use: `<html class="dark">` + `bg-bg text-text`

### Media query strategy

```css
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: oklch(0.15 0 0);
    --color-text: oklch(0.95 0 0);
  }
}
```

### With Tailwind's dark: variant

```html
<div class="bg-white dark:bg-slate-900">
```

---

## Custom Animations

```css
@theme {
  --animate-fade-in: fade-in 0.3s ease-out;
  
  @keyframes fade-in {
    from { opacity: 0; transform: translateY(-4px); }
    to { opacity: 1; transform: translateY(0); }
  }
}
```

Use: `animate-fade-in`

---

## Using Theme Variables

### In CSS

```css
.custom-element {
  color: var(--color-primary);
  padding: var(--spacing-4);
  border-radius: var(--radius-lg);
}
```

### In Arbitrary Values

```html
<div class="rounded-[calc(var(--radius-xl)-1px)]">
```

### In JavaScript

```js
const styles = getComputedStyle(document.documentElement);
const primary = styles.getPropertyValue('--color-primary');
```

---

## Variable References

Use `inline` when referencing other variables:

```css
@theme inline {
  --font-sans: var(--font-inter);
}
```

Without `inline`, CSS variable resolution may fail due to scope.

---

## Sharing Themes

**packages/brand/theme.css**:
```css
@theme {
  --color-brand: oklch(0.62 0.21 255);
  --font-brand: "Acme", sans-serif;
}
```

**app/globals.css**:
```css
@import "tailwindcss";
@import "../packages/brand/theme.css";
```

---

## Common Patterns

### Semantic Color System

```css
@theme {
  /* Primitives */
  --color-blue-500: oklch(0.62 0.21 255);
  --color-slate-900: oklch(0.21 0.03 265);
  
  /* Semantic (reference primitives) */
  --color-primary: var(--color-blue-500);
  --color-foreground: var(--color-slate-900);
}
```

### Responsive Container Widths

```css
@theme {
  --container-prose: 65ch;
  --container-content: 72rem;
  --container-wide: 90rem;
}
```

Use: `max-w-prose`, `max-w-content`, `max-w-wide`

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Utilities not generated | Ensure `@theme` is at top level, not nested |
| Variables not resolving | Use `@theme inline` for cross-references |
| Dark mode not working | Check class on `<html>` or media query config |
| Build errors | Verify `@import "tailwindcss"` is first import |

---

## Reference

- [Theme Variables](https://tailwindcss.com/docs/theme)
- [Installation Guides](https://tailwindcss.com/docs/installation)
- [Dark Mode](https://tailwindcss.com/docs/dark-mode)
