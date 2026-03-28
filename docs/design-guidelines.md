# Design Guidelines
# Prompt Vault Notepad

**Last Updated:** 2026-03-28

---

## 1. Design Philosophy

Prompt Vault uses a "premium dark-capable utility" aesthetic: clean surfaces, a restrained color palette, purposeful accents, and smooth transitions. The design should feel fast and focused — it is a tool, not a showcase.

Key principles:
- **Token-first**: no hardcoded color values anywhere in component styles
- **Contextual color**: semantic tokens (success, danger, accent) are used situationally, not decoratively
- **Motion with intent**: transitions indicate state changes; no gratuitous animation
- **Typography hierarchy**: three fonts, each with a specific role

---

## 2. Color System

All colors are defined as CSS custom properties and change with the active theme. There are no hardcoded hex values in component styles.

### Light Theme Palette

| Token | Value | Usage |
|-------|-------|-------|
| `--c-bg-base` | `#F0F2F5` | Page background |
| `--c-bg-surface` | `#FFFFFF` | Cards, sidebar, toolbar |
| `--c-bg-raised` | `#F7F8FA` | Hover backgrounds, icon buttons |
| `--c-bg-hover` | `#EEF0F4` | Note list item hover |
| `--c-bg-input` | `#F5F6F8` | Input fields |
| `--c-bg-overlay` | `rgba(0,0,0,.35)` | Modal backdrop |
| `--c-text-primary` | `#1A1D26` | Body text, headings |
| `--c-text-secondary` | `#5A5F72` | Labels, descriptions |
| `--c-text-muted` | `#8E93A6` | Placeholders, metadata |
| `--c-text-inverse` | `#FFFFFF` | Text on accent backgrounds |
| `--c-border` | `#DFE1E6` | Default borders |
| `--c-border-hover` | `#C1C5CE` | Borders on hover |
| `--c-accent` | `#4F6EF7` | Primary interactive color |
| `--c-accent-hover` | `#3D5BD9` | Accent hover state |
| `--c-accent-subtle` | `rgba(79,110,247,.08)` | Accent tint backgrounds |
| `--c-accent-glow` | `rgba(79,110,247,.18)` | Box shadows on accent elements |
| `--c-success` | `#22C55E` | Save indicator, success toasts |
| `--c-danger` | `#EF4444` | Delete button, error toasts |
| `--c-warning` | `#F59E0B` | System tag color |

### Dark Theme Palette

| Token | Value | Usage |
|-------|-------|-------|
| `--c-bg-base` | `#0F1117` | Page background |
| `--c-bg-surface` | `#1A1D28` | Cards, sidebar, toolbar |
| `--c-bg-raised` | `#22263A` | Raised elements |
| `--c-bg-hover` | `#2A2F42` | Note list item hover |
| `--c-bg-input` | `#1E2130` | Input fields |
| `--c-bg-overlay` | `rgba(0,0,0,.6)` | Modal backdrop |
| `--c-text-primary` | `#E8EAF0` | Body text |
| `--c-text-secondary` | `#9BA0B4` | Labels |
| `--c-text-muted` | `#636880` | Placeholders, metadata |
| `--c-accent` | `#6C8AFF` | Primary interactive color (brighter for dark contrast) |
| `--c-accent-hover` | `#8DA4FF` | Accent hover |
| `--c-success` | `#34D399` | Success state |
| `--c-danger` | `#F87171` | Error/delete state |
| `--c-warning` | `#FBBF24` | Warning/system state |

### Tag Colors

| Tag | Light Text | Light Background | Dark Text | Dark Background |
|-----|-----------|-----------------|-----------|----------------|
| Prompt | `#6366F1` (indigo) | `rgba(99,102,241,.08)` | `#818CF8` | `rgba(129,140,248,.1)` |
| ACC | `#0EA5E9` (sky blue) | `rgba(14,165,233,.08)` | `#38BDF8` | `rgba(56,189,248,.1)` |
| Khác | `#EC4899` (pink) | `rgba(236,72,153,.08)` | `#F472B6` | `rgba(244,114,182,.1)` |
| System | `#F59E0B` (amber) | `rgba(245,158,11,.08)` | `#FBBF24` | `rgba(251,191,36,.1)` |

---

## 3. Typography

Three typefaces, each with a distinct role:

| Font | Role | Weights Used |
|------|------|-------------|
| **Be Vietnam Pro** | Primary UI font — body text, labels, buttons, titles | 400, 500, 600, 700 |
| **JetBrains Mono** | Technical/monospace — logo text, timestamps, tag badges, code areas, login labels, keyboard shortcuts | 400, 500, 600 |
| **Times New Roman** | Note content — the prompt editor textarea | System font, normal weight |

### Type Scale

| Element | Font | Size | Weight | Line Height |
|---------|------|------|--------|-------------|
| Login logo | JetBrains Mono | 1.4rem | 700 | — |
| Note editor title | Be Vietnam Pro | 1.6rem | 700 | — |
| Empty state title | Be Vietnam Pro | 1.1rem | 600 | — |
| Note list title | Be Vietnam Pro | 0.88rem | 600 | — |
| Button text | Be Vietnam Pro | 0.80–0.95rem | 600–700 | — |
| Note preview | Be Vietnam Pro | 0.75rem | 400 | 1.5 |
| Meta / timestamps | JetBrains Mono | 0.65–0.76rem | 400 | — |
| Tag badges | JetBrains Mono | 0.60–0.72rem | 600 | — |
| Note content textarea | Times New Roman | 1.05rem | 400 | 1.9 |
| ACC content textarea | JetBrains Mono | 0.88rem | 400 | 1.9 |
| Status bar | JetBrains Mono | 0.68rem | 400 | — |
| Base font size | — | 15px | — | — |

---

## 4. Spacing and Shape

### Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `--radius` | `14px` | Editor card, modals |
| `--radius-sm` | `10px` | Buttons, input fields, note items, login card |
| `--radius-xs` | `6px` | Icon buttons, keyboard shortcut badges |
| `--radius-full` | `9999px` | Tag filter buttons, tag badges, toast icons, status dot |

### Shadows

| Token | Value | Usage |
|-------|-------|-------|
| `--shadow-xs` | `0 1px 2px rgba(0,0,0,.04)` | Subtle hover elevation |
| `--shadow-sm` | `0 1px 4px rgba(0,0,0,.06)` | Editor card resting state |
| `--shadow-md` | `0 4px 16px rgba(0,0,0,.08)` | Sidebar |
| `--shadow-lg` | `0 8px 32px rgba(0,0,0,.1)` | Toast notifications |
| `--shadow-xl` | `0 16px 48px rgba(0,0,0,.12)` | Login card, modals |

Dark theme shadows use higher opacity values (`.2`–`.4` range) due to the darker base.

### Layout

| Token | Value | Usage |
|-------|-------|-------|
| `--sidebar-w` | `320px` | Sidebar width (desktop) |
| Sidebar (tablet) | `280px` | At 1024px breakpoint |
| Sidebar (mobile) | `300px` | Fixed overlay on mobile |

---

## 5. Motion and Transitions

| Token | Duration and Easing | Usage |
|-------|-------------------|-------|
| `--transition-fast` | `0.15s cubic-bezier(.4,0,.2,1)` | Button hover, border color, box-shadow |
| `--transition-base` | `0.25s cubic-bezier(.4,0,.2,1)` | Modal open/close, sidebar, theme icon |
| `--transition-slow` | `0.40s cubic-bezier(.4,0,.2,1)` | Background color, text color (theme switch) |

The `cubic-bezier(.4,0,.2,1)` curve is Material Design's "Standard" easing — slightly fast out, slightly slow in.

### Named Animations

| Animation | Duration | Usage |
|-----------|----------|-------|
| `spin` | `0.8s linear infinite` | Loading spinner |
| `pulse` | `2s infinite` | Saved state indicator dot |
| `shake` | `0.4s ease` | Login card on failed auth |
| `slideIn` | `0.25s ease` | Note list item entrance |
| `toastProgress` | `2.8s linear forwards` | Toast progress bar depletion |

---

## 6. Background Decoration

The page background uses two decorative layers:

**Layer 1 — Gradient orbs** (via `body::before`):
```css
background:
  radial-gradient(ellipse 600px 400px at 10% 15%, var(--c-accent-subtle), transparent),
  radial-gradient(ellipse 500px 500px at 90% 85%, var(--c-tag-other-bg), transparent);
```

**Layer 2 — Grid overlay** (via `body::after`):
```css
background-image:
  linear-gradient(var(--c-border) .5px, transparent .5px),
  linear-gradient(90deg, var(--c-border) .5px, transparent .5px);
background-size: 80px 80px;
opacity: .25;
```

Both layers use `pointer-events: none` and are purely decorative.

---

## 7. Component Patterns

### Active State (Note List Item)

```css
.note-item.active {
  background: var(--c-accent-subtle);
  border-color: var(--c-accent);
  box-shadow: inset 3px 0 0 var(--c-accent), var(--shadow-sm);
}
```

The `inset 3px 0 0` creates a left-edge accent bar — a common "selected item" visual cue.

### Button Variants

| Class | Style |
|-------|-------|
| `.btn` | Ghost — border + text, accent on hover |
| `.btn-accent` | Filled accent background |
| `.btn-danger` | Ghost with danger color on hover |
| `.icon-btn` | Square icon-only, 32×32px |
| `.create-btn` | Full-width filled accent, sidebar footer |
| `.login-btn` | Full-width filled accent, login card |

### Login Card Gradient Bar

The top edge of the login card uses a tri-color gradient matching the brand:
```css
background: linear-gradient(135deg, var(--c-accent), var(--c-tag-other), var(--c-warning));
```

---

## 8. Focus Styles

All focusable elements use a consistent focus ring:
```css
:focus-visible {
  outline: 2px solid var(--c-accent);
  outline-offset: 2px;
  border-radius: var(--radius-xs);
}
```

`focus-visible` ensures the ring only appears for keyboard navigation, not mouse clicks.

---

## 9. Text Selection

```css
::selection {
  background: var(--c-accent-glow);
  color: var(--c-text-primary);
}
```

Selected text uses the accent glow color as a background, maintaining readability across both themes.
