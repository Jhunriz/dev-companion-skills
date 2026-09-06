---
name: ui-ux
description: UI/UX design patterns, user experience standards, interaction design, accessibility, and visual consistency for this project. Trigger for "UI", "UX", "design", "user experience", "accessibility", "a11y", "responsive", "visual design", "interaction", "wireframe", "usability", "design system".
---

# UI/UX

Defines **this project's design and user experience standards**. Use for designing interfaces, reviewing UX flows, ensuring accessibility, or answering "how should this look and feel?"

## Design Principles

1. **Clarity** — Every element should have a clear purpose
2. **Consistency** — Reuse patterns, colors, spacing, and components
3. **Hierarchy** — Visual weight guides the user's attention
4. **Feedback** — Every action produces visible feedback
5. **Accessibility** — Design for all users, not the average user
6. **Performance** — Perceived speed matters as much as actual speed

## Visual Design System

### Color Palette
| Token | Usage |
|-------|-------|
| `primary` | Main brand color, CTAs, active states |
| `secondary` | Supporting elements, accents |
| `background` | Page backgrounds |
| `surface` | Card, modal, and panel backgrounds |
| `text-primary` | Main text |
| `text-secondary` | Muted text, labels |
| `border` | Dividers, input borders |
| `error` | Validation errors, destructive actions |
| `success` | Success states, confirmations |
| `warning` | Warnings, pending states |

### Typography
| Element | Size | Weight | Style |
|---------|------|--------|-------|
| H1 | 2rem | Bold | Heading |
| H2 | 1.5rem | Bold | Heading |
| H3 | 1.25rem | Semi-bold | Heading |
| Body | 1rem | Regular | Body |
| Small | 0.875rem | Regular | Body |
| Caption | 0.75rem | Medium | Caption |

### Spacing Scale
```
1 = 4px    2 = 8px    3 = 12px   4 = 16px
5 = 20px   6 = 24px   8 = 32px   10 = 40px
12 = 48px  16 = 64px  20 = 80px  24 = 96px
```

- Use spacing scale multiples, never arbitrary values
- **8px base unit** for all spacing decisions

## Component Design Rules

### Button Variants
| Variant | Usage |
|---------|-------|
| `primary` | Main CTA, submit actions |
| `secondary` | Secondary actions, cancel |
| `outline` | Tertiary actions, less prominent |
| `ghost` | Minimal actions, toolbar items |
| `destructive` | Delete, remove, dangerous actions |

### Form Design
- **Labels above inputs** (not placeholder-only)
- **Inline validation** — show errors on blur, not on type
- **Group related fields** with `FieldGroup` / `Field`
- **Show progress** for multi-step forms
- **Clear error messages** — what went wrong and how to fix it

### Layout Patterns
- **Page width**: Max-width container centered
- **Sidebar**: Fixed width, collapsible on mobile
- **Grid**: Responsive columns using CSS Grid or Tailwind `grid`
- **Cards**: Consistent padding, shadow, border-radius
- **Navigation**: Persistent, clear active state

## Interaction Design

### Motion Principles
- **Duration**: 150-300ms for micro-interactions, 300-500ms for transitions
- **Easing**: `ease-out` for entering, `ease-in` for leaving
- **Purpose**: All motion should serve a functional purpose
- **Reduced motion**: Respect `prefers-reduced-motion`

### Feedback States
| State | Visual |
|-------|--------|
| **Hover** | Slight color change, cursor pointer |
| **Active/Pressed** | Scale down slightly, darker color |
| **Focus** | Visible ring/border outline |
| **Disabled** | Reduced opacity, `not-allowed` cursor |
| **Loading** | Spinner, skeleton, or progress bar |
| **Success** | Green checkmark or green tint |
| **Error** | Red border, error icon, error message |

## Accessibility (a11y)

### WCAG 2.1 AA Compliance
- **Contrast ratio**: Minimum 4.5:1 for text, 3:1 for large text
- **Focus management**: Visible focus indicators on all interactive elements
- **Keyboard navigation**: All functionality reachable via keyboard
- **Screen readers**: Semantic HTML, ARIA labels, `aria-live` regions
- **Form accessibility**: Labels associated with inputs, error messages linked

### Semantic HTML
```tsx
// Good: semantic structure
<main>
  <h1>Page Title</h1>
  <nav aria-label="Main navigation">...</nav>
  <section>...</section>
  <article>...</article>
  <aside>...</aside>
</main>

// Bad: div soup
<div className="main">
  <div className="title">Page Title</div>
  <div className="nav">...</div>
</div>
```

### ARIA Patterns
- Use `aria-label` for icon-only buttons
- Use `aria-describedby` linking inputs to error messages
- Use `aria-live="polite"` for dynamic content updates
- Use `role="alert"` for error messages
- Never use `aria-hidden="true"` on focusable elements

## Responsive Design

### Breakpoints
| Breakpoint | Width | Typical Device |
|------------|-------|----------------|
| `sm` | 640px | Large phones |
| `md` | 768px | Tablets |
| `lg` | 1024px | Laptops |
| `xl` | 1280px | Desktops |
| `2xl` | 1536px | Large desktops |

### Responsive Patterns
```tsx
// Mobile-first approach
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4">
  {items}
</div>

// Hide/show based on viewport
<div className="hidden md:block">Desktop content</div>
```

## UX Writing Guidelines

### Button Labels
- **Action-oriented**: "Save Changes" not "Submit"
- **Specific**: "Delete Project" not "Delete"
- **Consistent**: Use same verb tense throughout

### Error Messages
- **What happened**: "Unable to save your profile"
- **Why**: "The email is already in use"
- **How to fix**: "Use a different email or sign in"

### Empty States
- **Explain**: What the section is for
- **CTA**: Clear action to get started
- **Illustration**: Use visual cues where possible

## Design Review Checklist

- [ ] Visual hierarchy is clear (F-pattern or Z-pattern)
- [ ] Color contrast meets WCAG AA standards
- [ ] Touch targets are minimum 44x44px
- [ ] Spacing follows the 8px grid system
- [ ] Typography scale is consistent
- [ ] All interactive states are defined
- [ ] Responsive at all breakpoints
- [ ] Keyboard navigation works end-to-end
- [ ] Screen reader test passed
- [ ] Error messages are helpful and specific
- [ ] Loading states communicate progress
- [ ] Animations are purposeful and fast

## Quick Decisions

| Question | Answer |
|----------|--------|
| How to choose a color? | Use the design system tokens, never custom colors |
| How to handle empty states? | Explain + CTA + visual |
| How to write error messages? | What happened + Why + How to fix |
| How to design for mobile? | Mobile-first, single column, collapsible navigation |
| Where to store design tokens? | Tailwind config / CSS variables |
| How to ensure accessibility? | Semantic HTML + ARIA + keyboard testing |
| How long should animations be? | 150-300ms micro-interactions, 300-500ms transitions |

---

**Customize**: Update the color palette, typography, and spacing scale to match your brand guidelines. Adjust breakpoints and component rules to fit your design system.
