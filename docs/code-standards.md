# Code Standards
# Prompt Vault Notepad

**Last Updated:** 2026-03-28

---

## 1. General Principles

1. **Single-file discipline** — All code lives in `index.html`. Do not introduce separate `.css` or `.js` files unless the project migrates to a build-tool workflow.
2. **No hardcoded colors** — Every color value must come from a CSS custom property (`--c-*`). Raw hex or rgb values in component styles are a standards violation.
3. **No innerHTML with user data** — User-generated strings must be escaped before insertion into HTML. Use the `escHtml()` utility or set `element.textContent` directly.
4. **IIFE isolation** — All JavaScript is wrapped in an immediately invoked function expression `(function(){ 'use strict'; ... })()` to avoid polluting the global scope.
5. **Strict mode** — `'use strict';` is declared at the top of the IIFE.

---

## 2. Naming Conventions

### CSS Classes

- Use **kebab-case** for all class names: `.note-item`, `.sidebar-header`, `.tag-btn`
- BEM-style partial naming is used but not strictly enforced:
  - Block: `.editor-card`
  - Element: `.editor-card .title-input`
  - Modifier: `.tag-btn.active`, `.btn.btn-accent`, `.toast.success`
- State classes are bare adjectives appended directly: `.active`, `.show`, `.open`, `.hidden`, `.shake`, `.fade-out`

### CSS Custom Properties (Design Tokens)

All tokens use the `--c-` prefix followed by a semantic category:

```
--c-{category}-{variant}
```

Categories: `bg`, `text`, `border`, `accent`, `success`, `danger`, `warning`, `tag`

Examples:
- `--c-bg-base`, `--c-bg-surface`, `--c-bg-raised`, `--c-bg-hover`, `--c-bg-input`
- `--c-text-primary`, `--c-text-secondary`, `--c-text-muted`, `--c-text-inverse`
- `--c-accent`, `--c-accent-hover`, `--c-accent-subtle`, `--c-accent-glow`
- `--c-tag-prompt`, `--c-tag-prompt-bg`, `--c-tag-acc`, `--c-tag-acc-bg`

Shadow and shape tokens do not use the `--c-` prefix:
- `--shadow-xs`, `--shadow-sm`, `--shadow-md`, `--shadow-lg`, `--shadow-xl`
- `--radius`, `--radius-sm`, `--radius-xs`, `--radius-full`
- `--transition-fast`, `--transition-base`, `--transition-slow`
- `--sidebar-w`

### HTML Element IDs

IDs use **camelCase**: `loadingScreen`, `loginCard`, `noteList`, `editorArea`, `importFileInput`

When an ID corresponds to a single interactive element, it ends with the element type where helpful: `loginForm`, `loginUser`, `loginPass`, `deleteModal`, `importModal`, `themeIcon`

### JavaScript Variables and Functions

- Variables: **camelCase** — `activeNoteId`, `searchQuery`, `pendingImportData`
- Functions: **camelCase** — `renderNoteList()`, `hashPassword()`, `handleLogin()`
- Constants: **SCREAMING_SNAKE_CASE** — `STORAGE_KEY`, `VALID_USER`, `VALID_HASH`
- DOM shorthand helper: `const $ = id => document.getElementById(id)` — single character name by convention

### JavaScript Naming Patterns

- Handler functions are prefixed with `handle`: `handleLogin`, `handleLogout`, `handleSearch`, `handleImportFile`, `handleTagFilter`
- Render functions are prefixed with `render`: `renderNoteList`, `renderEditor`, `renderEmptyState`
- Boolean-returning checks use `check` or `is`: `checkAuth`
- Show/hide pairs use `show`/`close` or `show`/`hide`: `showApp`, `showLogin`, `closeSidebar`, `toggleSidebar`

---

## 3. CSS Architecture

### Token-First Approach

All component styles reference tokens only. The dark theme is implemented entirely by redefining token values on `[data-theme="dark"]`. No component rule is duplicated for dark mode.

```css
/* Correct */
.note-item.active {
  background: var(--c-accent-subtle);
  border-color: var(--c-accent);
}

/* Incorrect — hardcoded color */
.note-item.active {
  background: rgba(79,110,247,.08);
}
```

### Transitions

All interactive state changes (hover, focus, active) use transition properties referencing transition tokens:

```css
transition: all var(--transition-fast);          /* 0.15s */
transition: background var(--transition-base);   /* 0.25s */
transition: opacity var(--transition-slow);      /* 0.40s */
```

### Z-Index Layers

| Layer | Value | Element |
|-------|-------|---------|
| Grid background | 0 | `body::after` |
| App content | 1 | `.app` |
| Mobile sidebar | 9–10 | `.sidebar-backdrop`, `.sidebar` |
| Login screen | 200 | `.login-screen` |
| Toast container | 300 | `.toast-container` |
| Loading screen | 999 | `.loading-screen` |

### Responsive Breakpoints

| Breakpoint | Behavior |
|------------|----------|
| `max-width: 1024px` | Sidebar width reduced to 280px |
| `max-width: 768px` | Sidebar becomes a fixed overlay; hamburger menu shown; button labels hidden |
| `max-width: 480px` | Login card padding reduced; toolbar buttons smaller |

### Scrollbar Styling

Thin custom scrollbars are applied to scrollable containers:

```css
::-webkit-scrollbar { width: 3px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: var(--c-border); border-radius: 4px; }
```

The tags bar hides its scrollbar: `.tags-bar::-webkit-scrollbar { display: none; }`

---

## 4. JavaScript Patterns

### IIFE Wrapper

```javascript
(function(){
  'use strict';
  // all code here
})();
```

### DOM Access

All DOM elements are referenced once at the top of the IIFE using the `$` helper and stored in `const` variables. Elements that are created dynamically (title input, content input, tag select) are accessed via `$('id')` at the point of use.

```javascript
const $ = id => document.getElementById(id);

// Static refs — declared once
const noteList = $('noteList');
const editorArea = $('editorArea');

// Dynamic refs — called each time after render
const titleInput = $('titleInput');
```

### Debouncing

Auto-save uses 500ms debounce. Search uses 300ms debounce. The `debounce` utility returns a new function each call, so it must be used with a stable reference:

```javascript
// Correct — stable reference created once per editor mount
const debouncedUpdate = debounce(() => updateActiveNote(), 500);
titleInput.addEventListener('input', debouncedUpdate);

// Correct — module-level for search
const handleSearch = debounce(function(val) { ... }, 300);
```

### Async Authentication

`hashPassword` is `async` and uses `await`. `handleLogin` is also `async`:

```javascript
async function hashPassword(pwd) {
  const data = new TextEncoder().encode(pwd);
  const buf  = await crypto.subtle.digest('SHA-256', data);
  return Array.from(new Uint8Array(buf))
    .map(b => b.toString(16).padStart(2, '0'))
    .join('');
}
```

### XSS Prevention

The `escHtml` function uses the DOM's own serializer, which is more reliable than a regex-based approach:

```javascript
function escHtml(s) {
  const d = document.createElement('div');
  d.textContent = s;
  return d.innerHTML;
}
```

All user-controlled strings (`note.title`, `note.content`) are passed through `escHtml` before being embedded in template literals used for `innerHTML` assignments.

### Event Delegation

The note list uses event delegation to avoid attaching a listener to each note card:

```javascript
noteList.addEventListener('click', function(e) {
  const item = e.target.closest('.note-item');
  if (item) selectNote(item.dataset.id);
});
```

### Dynamic HTML Rendering

The editor is fully re-rendered when a note is selected or the tag changes. The `renderEditor` function writes to `editorArea.innerHTML` and then attaches fresh event listeners. This avoids stale closure issues.

### Error Handling

`localStorage` operations are wrapped in try/catch. JSON parsing failures silently reset to an empty array:

```javascript
function loadNotes() {
  try {
    notes = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
  } catch {
    notes = [];
  }
}
```

`saveNotes` catches quota errors and notifies the user:

```javascript
function saveNotes() {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(notes));
  } catch {
    showToast('Bộ nhớ đã đầy! Hãy xuất dữ liệu ra file.', 'error');
  }
}
```

---

## 5. HTML Structure Standards

- The `<html>` element carries `lang="vi"` and `data-theme="light"` as the initial default
- The `data-theme` attribute is updated at runtime by `setTheme()`
- All screens are present in the DOM at load time, toggled via the `.hidden` utility class (`display: none !important`)
- Modals use `.modal-overlay` + `.show` class for opacity transitions
- All inline SVG icons use `viewBox="0 0 24 24"` with `fill="none"` and `stroke="currentColor"` for theme-aware coloring

---

## 6. Security Standards

| Concern | Implementation |
|---------|---------------|
| Password storage | SHA-256 hex digest stored in source; plain text never appears |
| Password comparison | Async hash computed at login time and compared to stored hash |
| Session management | `sessionStorage` flag; cleared on tab close and explicit logout |
| XSS prevention | All user data escaped with `escHtml()` before `innerHTML` assignment |
| No `eval` or dynamic code execution | None used anywhere |
| No external script loading | Only Google Fonts CSS is loaded from CDN |

---

## 7. Localization

- All user-visible text is in Vietnamese
- Date formatting uses `vi-VN` locale: `d.toLocaleDateString('vi-VN', { day: '2-digit', month: '2-digit', year: 'numeric' })`
- Relative timestamps are hardcoded Vietnamese strings: "Vừa xong", "phút trước", "giờ trước", "Hôm qua"
- Documentation files are in English
