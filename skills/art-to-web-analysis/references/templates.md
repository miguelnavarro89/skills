# Section Structure Templates

## Container

```
- **Edge behavior**: Full-bleed | Contained | Partial-bleed (specify sides)
- **Width**: Full viewport | XXXXpx | XX%
- **Height**: XXXpx | XXvh | Auto
- **Padding**: Top XXpx, Right XXpx, Bottom XXpx, Left XXpx
- **Margin**: Top XXpx, Bottom XXpx
- **Background**: [Type + exact values]
  - If solid: #XXXXXX
  - If gradient: Direction, stops (color + position)
  - If image: Object-fit, object-position, overlay (color + opacity)
```

## Layout

```
- **Model**: Flexbox | Grid | Block | Absolute positioning
- **Direction**: Row | Column
- **Alignment (main)**: Start | Center | End | Space-between | Space-around
- **Alignment (cross)**: Start | Center | End | Stretch
- **Gap**: XXpx
- **If grid**: Column/row definitions
- **Wrap**: Yes | No
```

## Element

```
- **Tag**: Semantic suggestion (nav, header, h1, p, button, img, etc.)
- **Position in parent**: Describe spatial relationship
- **Dimensions**: Width × Height (exact or auto)
- **Content**:
  - Text: Exact copy
  - Image: "[Image placeholder]" (skip content description)
  - Icon: Describe shape, style, meaning
- **Typography** (if text):
  - font-family: Name or category (serif, sans-serif, monospace)
  - font-size: XXpx
  - font-weight: XXX (100-900)
  - line-height: X.X or XXpx
  - letter-spacing: XXpx | XXem | normal
  - text-transform: none | uppercase | capitalize
  - text-align: left | center | right
  - Special: `<sup>` | `<sub>` | font-variant: small-caps | none
- **Colors**:
  - Text: #XXXXXX
  - Background: #XXXXXX or transparent
  - Border: #XXXXXX
- **Border**:
  - Width: XXpx
  - Style: Solid | Dashed | None
  - Radius: XXpx (per corner if different)
- **Shadow**: X-offset Y-offset Blur Spread Color
- **Opacity**: X.X (if not 1)
- **Transform**: If any (rotate, scale, translate)
```

## Interactive States

```
**[Element name] - :hover**
- background-color: #XXXXXX → #YYYYYY
- color: #XXXXXX → #YYYYYY
- transform: none → scale(1.02)
- transition: 0.2s ease

**[Element name] - :focus**
- outline: 2px solid #XXXXXX
- box-shadow: 0 0 0 3px rgba(X,X,X,0.3)

**[Element name] - :disabled**
- opacity: 0.5
- cursor: not-allowed

**[Element name] - loading**
- Content replaced with: spinner | skeleton | shimmer
- Animation: [describe]

**[Element name] - error**
- border-color: #XXXXXX (red variant)
- Error message: "[exact text]"
```

## Icons Table

```
| Element | Platform/Shape | Style | Size | Color | Stroke Width |
|---------|----------------|-------|------|-------|--------------|
| [location] | [Brand name OR shape] | filled/outlined/circled | 24×24 | #XXXXXX | 2px |
```

## Animations (video only)

### Animation

```
#### Animation: [Descriptive Name]
- **Target**: Which element(s) animate
- **Trigger**: Page load | Scroll | Hover | Click | Time-based | Continuous
  - Scroll: Element enters viewport at XX% visibility
  - Delay: XXms before start
- **Timing**: XXXms, Easing (Linear | Ease | Cubic-bezier), Iterations, Direction
- **Keyframes**:
  | Progress | Property | Value |
  |----------|----------|-------|
  | 0% | [property] | [start value] |
  | 100% | [property] | [end value] |
- **Stagger** (if multiple): XXms delay, Sequential | Reverse | Random
```

### Transition

```
#### Transition: [Element/Interaction Name]
- **Property**: What animates
- **Duration**: XXms
- **Easing**: Function
- **From → To**: Start value → End value
```

### Scroll-Linked

```
#### Scroll-Linked: [Name]
- **Element**: What moves/changes
- **Scroll range**: [start] to [end]
- **Property → Value range**: [start value] → [end value]
```

## Report Frontmatter

```yaml
---
name: Design spec - [brief description]
overview: Visual specification for [what's being analyzed]
todos:
  - id: section-name
    content: Implement [section name]
    status: pending
  # One todo per section identified
isProject: false
---
```
