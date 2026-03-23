---
name: manr:art-to-web-analysis
description: Analyze visual art, mockups, and designs (images/videos) to produce pixel-perfect web implementation specs. Use when converting design artwork to CSS/HTML specifications, reverse-engineering visual interfaces, or creating implementation specs from screenshots or design files.
---

# Art to Web Analysis

Produce specifications so precise any developer—without seeing original—can implement pixel-perfect web replica.

## Core Principles

1. **Framework-agnostic**: CSS property names, semantic HTML. No Tailwind, React, Vue syntax.
2. **Pixel-perfect**: Exact measurements, colors, ratios. Uncertain → ranges + estimate.
3. **Only visible**: Document what's seen. No assumptions about responsive/hidden states.
4. **Top-to-bottom, outer-to-inner**: Visual reading order, then nested drill-down.
5. **Blind assumption**: Reader has zero visual access.

## Technical Terminology

**Use freely:**
- CSS properties: `letter-spacing`, `font-weight`, `line-height`, `gap`, `padding`, `margin`
- CSS values: `0.1em`, `400`, `#XXXXXX`, `rgba()`, `translateX()`
- CSS layout: `flexbox`, `grid`, `absolute`, `fixed`, `sticky`
- HTML tags: `<sup>`, `<sub>`, `<nav>`, `<button>`, `<input>`
- CSS functions: `cubic-bezier()`, `linear-gradient()`

**Avoid:**
- Tailwind: `tracking-wide`, `flex-row`, `gap-4`, `text-lg`
- React/Vue/Svelte: `<Button />`, `v-if`, `{#each}`
- Framework patterns: "use a component", "add a hook"

## Clarification

Use AskQuestion tool to resolve ambiguity:
- Section boundaries unclear? Ask user to confirm logical divisions
- Component reuse uncertain? Ask if similar elements are same component
- Interactive behavior ambiguous? Ask about intended states/triggers
- Purpose unclear? Ask what the section/element is for

## Section Structure

Each section includes:
- **Container**: edge behavior, dimensions, padding, background
- **Layout**: model, direction, alignment, gap
- **Elements**: tag, position, dimensions, content, typography, colors, border, shadow
- **Icons** (if present): shape description, size, color, stroke width
- **Animations** (video only): target, trigger, timing, keyframes, stagger, transitions

For detailed templates, see [templates.md](references/templates.md).

## Measurement Guidelines

- Colors: exact hex
- Sizes: nearest 4px
- Uncertainty: "~XXpx (estimated)"
- Common ratios: 16:9, 4:3, 1:1, 3:2

## What to Document

- Every visible element
- Exact text content (character-by-character accuracy)
- All colors, spacing, typography
- Visual hierarchy, nesting
- Icons (describe visual form)
- States only if shown

## What to Skip

- Image content (use category-tagged placeholders: "[Product photo]", "[Hero banner]", "[Avatar]")
- Assumed responsive behavior
- Assumed hover/focus states (only document if visible)
- Implementation details
- Accessibility assumptions

## Typography Edge Cases

Document explicitly when present:
- **Superscripts/subscripts**: M`<sup>`c`</sup>`, 1`<sup>`st`</sup>`, TM, ®
- **Small caps**: `font-variant: small-caps`
- **Mixed case patterns**: note exact `text-transform` value
- **Ligatures**: `font-variant-ligatures` value if non-default

## Text Accuracy

Critical for implementation fidelity:
- Copy exact strings character-by-character
- Preserve line breaks as visually rendered
- Include all punctuation and symbols (+, >, <, ©, •, |)
- Note year values exactly as shown
- Include country/region qualifiers
- Document trailing/leading whitespace if significant

## Icon Identification

**Platform/brand icons**: Name explicitly (Instagram, TikTok, Pinterest, Facebook, YouTube, X/Twitter, LinkedIn)
- Style: filled, outlined, monochrome, circled, bare, branded colors
- Order: Document left-to-right sequence
- If uncertain: Describe distinguishing shape

**Generic icons**: Describe geometric form precisely, note stroke vs fill style.

## Content Hierarchy

For grouped content (navigation, footer columns, lists):
- **Headings**: Document separately from items, note if styled differently
- **Items**: List in visual order
- **Format**: "Heading: [text]" → "Items: [A], [B], [C]"
- **Nesting**: Indicate parent-child relationships clearly

## Interaction States

If multiple states visible, document each:

| State | When to Document |
|-------|------------------|
| **:hover** | Mouse over element |
| **:focus** | Keyboard focus / input active |
| **:active** | During click/tap |
| **:disabled** | Grayed out, non-interactive |
| **:visited** | Link previously clicked |
| **empty** | No content/data |
| **loading** | Spinner, skeleton, shimmer |
| **error** | Validation failure, API error |

For each: document which properties change + transition timing if animated.

## Video Analysis

- Watch multiple times at normal + slow speed
- Note every motion, transition, state change
- Capture timing precisely (video timestamps)
- Document trigger conditions
- Describe easing (snappy, smooth, bouncy)

## Quality Checklist

### Structure
- [ ] Every visible element documented
- [ ] Sections top-to-bottom
- [ ] Elements outer-to-inner
- [ ] Column/section headings documented separately from items

### Typography & Text
- [ ] All text strings verified character-by-character
- [ ] Typography fully specified (family, size, weight, line-height, letter-spacing, transform)
- [ ] Special typography noted (superscripts, small caps, mixed case)
- [ ] Year values, country codes, symbols exact

### Visual Properties
- [ ] All colors have exact hex
- [ ] Spacing for all gaps/padding
- [ ] Images as category-tagged placeholders

### Icons
- [ ] Platform/brand icons identified by name
- [ ] Icon style documented (filled, outlined, circled)
- [ ] Icon order documented (left-to-right)

### Animations (video only)
- [ ] Animations with timing/keyframes
- [ ] Playback controls documented if visible

### Interaction States (if visible)
- [ ] All visible states documented
- [ ] State transitions noted with timing

### Standards
- [ ] Uses CSS property names, avoids framework-specific syntax
- [ ] No unseen state assumptions

## References

CSS property lookup:
- **CSS Reference**: https://developer.mozilla.org/en-US/docs/Web/CSS/Reference
- **Font features**: https://developer.mozilla.org/en-US/docs/Web/CSS/font-feature-settings
- **Transitions/Animations**: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions
