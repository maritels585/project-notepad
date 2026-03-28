# System Architecture
# Prompt Vault Notepad

**Last Updated:** 2026-03-28

---

## 1. High-Level Overview

Prompt Vault is a **client-only, single-file web application**. There is no server, no API, no database, and no build pipeline. All logic, presentation, and data storage run entirely within the user's browser.

```
┌─────────────────────────────────────────────┐
│                  Browser                     │
│                                             │
│  ┌──────────┐    ┌────────────────────────┐ │
│  │index.html│───▶│  JavaScript Runtime    │ │
│  │  (CSS +  │    │  (IIFE, ES2020)        │ │
│  │  HTML +  │    └──────────┬─────────────┘ │
│  │   JS)    │               │               │
│  └──────────┘    ┌──────────▼─────────────┐ │
│                  │  Browser Storage APIs   │ │
│                  │  localStorage           │ │
│                  │  sessionStorage         │ │
│                  └────────────────────────┘ │
│                                             │
│  External: Google Fonts CDN (CSS only)      │
└─────────────────────────────────────────────┘
```

---

## 2. Application Layers

The single file is logically divided into three layers:

```
index.html
├── Presentation Layer   — <style> block (CSS variables, component styles)
├── Structure Layer      — <body> HTML (screens, components, modals)
└── Logic Layer          — <script> IIFE (state, auth, CRUD, render, events)
```

These layers are not separated into files; all three are co-located for portability.

---

## 3. Startup and Auth Flow

```
Browser opens index.html
        │
        ▼
setTheme(getTheme())          ← Apply theme immediately from localStorage
        │                        (prevents flash of wrong theme)
        ▼
checkAuth()
        │
        ├── setTimeout(600ms)  ← Minimum loading screen duration
        │
        ▼
loadingScreen.fade-out
        │
        ├── sessionStorage.getItem('pv_auth') === 'true' ?
        │           │                    │
        │          YES                   NO
        │           │                   │
        │           ▼                   ▼
        │       showApp()          showLogin()
        │           │
        │       loadNotes()
        │       renderNoteList()
        │
        └── (app is ready)
```

### Login Sequence

```
User submits login form
        │
        ▼
handleLogin(e)
        │
        ├── Validate: user and pass not empty
        │       └── if empty: shake animation + error message
        │
        ▼
hashPassword(pass) — async SHA-256 via Web Crypto API
        │
        ▼
user === VALID_USER && hash === VALID_HASH ?
        │                   │
       YES                  NO
        │                   │
        ▼                   ▼
sessionStorage          shake animation
.setItem('pv_auth',     + error message
'true')                 + clear password field
        │
        ▼
showApp() → loadNotes() → renderNoteList()
```

---

## 4. State Architecture

The application uses a flat, module-level state pattern (not a framework):

```
State
├── notes: Note[]           In-memory copy of localStorage data
├── activeNoteId: string    Currently open note (null = empty state)
├── activeTag: string       Current sidebar tag filter ('all' | 'prompt' | 'acc' | 'other' | 'system')
├── searchQuery: string     Current search text
└── pendingImportData: Note[] | null   Staged import payload awaiting confirmation
```

State mutations follow this pattern:
1. Mutate the in-memory `notes` array
2. Call `saveNotes()` to persist to `localStorage`
3. Call `renderNoteList()` and/or `renderEditor()` to update the DOM

There is no reactive data binding, virtual DOM, or state management library. All renders are triggered explicitly.

---

## 5. Storage Architecture

### localStorage

```
Key: 'pv_notes'
Value: JSON array of Note objects
Limit: ~5MB (browser default)

Key: 'pv_theme'
Value: 'light' | 'dark'
```

Data is read once on `loadNotes()` (called on `showApp()`), maintained in-memory as `notes[]`, and written on every mutation via `saveNotes()`.

### sessionStorage

```
Key: 'pv_auth'
Value: 'true'
Lifetime: Until tab/browser close
```

The session flag is a simple string presence check. There is no token, no expiry time, and no CSRF protection (not applicable for a local-only app).

---

## 6. Rendering Architecture

The application uses **imperative DOM rendering** via `innerHTML` assignment. There is no templating engine.

### Render Zones

| Zone | Element | Rendered By |
|------|---------|-------------|
| Note list | `#noteList` | `renderNoteList()` |
| Editor area | `#editorArea` | `renderEditor(note)` or `renderEmptyState()` |

### Render Triggers

| Trigger | Functions Called |
|---------|-----------------|
| Login success | `loadNotes()`, `renderNoteList()` |
| Create note | `renderEditor(note)`, `renderNoteList()` |
| Select note | `renderEditor(note)`, `renderNoteList()` |
| Auto-save (500ms debounce) | `renderNoteList()` |
| Search input (300ms debounce) | `renderNoteList()` |
| Tag filter click | `renderNoteList()` |
| Tag change in editor | `renderEditor(note)` (full re-render to toggle ACC mode) |
| Delete note | `renderEmptyState()`, `renderNoteList()` |
| Import confirm | `renderEmptyState()`, `renderNoteList()` |

### ACC Editor Re-Render

When the user changes the tag select to or from `acc`, `renderEditor(note)` is called immediately (not debounced) to swap the plain `<textarea>` for the lined-editor wrapper or vice versa:

```javascript
tagSelect.addEventListener('change', () => {
  updateActiveNote();
  const n = notes.find(x => x.id === activeNoteId);
  if (n) renderEditor(n);  // full re-render
});
```

---

## 7. Theme System Architecture

Theme is controlled by a single `data-theme` attribute on `<html>`. All component colors reference CSS custom properties. Switching themes requires only changing this one attribute.

```
data-theme="light"   → :root / [data-theme="light"] token set applies
data-theme="dark"    → [data-theme="dark"] token set overrides all --c-* tokens
```

Theme persistence:
1. On page load: `setTheme(getTheme())` reads `localStorage` and applies before first paint
2. On toggle: `setTheme(next)` updates the attribute and writes to `localStorage`

This prevents any flash of wrong theme (FOWT) because `setTheme()` is called synchronously before the loading screen fades.

---

## 8. ACC Line-Numbered Editor

ACC-tagged notes use a specialized editor layout:

```
.lined-editor
├── .line-numbers     ← Non-interactive div, renders "1\n2\n3\n..."
└── .content-textarea ← Standard <textarea> with JetBrains Mono font
```

**Line count calculation:**

```javascript
const lines = contentInput.value.split('\n').length;
const totalLines = Math.max(50, lines);  // minimum 50 lines always shown
```

**Scroll synchronization:**

```javascript
contentInput.addEventListener('scroll', syncLineScroll);

function syncLineScroll() {
  lineNumbers.scrollTop = contentInput.scrollTop;
}
```

Both the line-numbers div and the textarea share the same `line-height: 1.9` and `font-size: 0.88rem` in JetBrains Mono, ensuring pixel-perfect alignment.

---

## 9. Modal System

Two modals are defined in the HTML: `#deleteModal` and `#importModal`.

```
.modal-overlay         ← Full-screen backdrop with blur
    └── .modal         ← Centered card with animation

Toggle:
  show:   modal.classList.add('show')     → opacity 1, pointer-events auto, card slides up
  hide:   modal.classList.remove('show')  → opacity 0, pointer-events none
```

Modals close on:
- Explicit cancel/confirm button clicks
- `Escape` key press
- Click on the backdrop (outside the `.modal` element)

---

## 10. Toast Notification System

Toasts are dynamically created and appended to `#toastContainer`:

```
#toastContainer (fixed, bottom-right)
└── .toast.{type}          ← Dynamically created per notification
    ├── .toast-icon         ← ✓ / ✕ / ℹ character
    ├── <span>message</span>
    └── .toast-progress     ← CSS animation shrinks from 100% to 0 over 2.8s
```

Lifecycle:
1. Element created and appended
2. `requestAnimationFrame` double-rAF trick triggers CSS transition (opacity 0→1, translateY 20px→0)
3. After 2800ms: remove `.show` class
4. After 350ms additional: remove element from DOM

---

## 11. Keyboard Shortcut System

All shortcuts are registered in a single `keydown` listener on `document`:

```javascript
document.addEventListener('keydown', function(e) {
  if (e.ctrlKey || e.metaKey) {
    if (e.key === 'n')  { /* create note */ }
    if (e.key === 's')  { /* save */ }
    if (e.key === 'k')  { /* focus search */ }
    if (e.key === 'd')  { /* duplicate */ }
  }
  if (e.key === 'Delete' && /* not in input/textarea */) { /* open delete modal */ }
});
```

`e.metaKey` support means `Cmd` equivalents work on macOS.

---

## 12. Mobile Sidebar Architecture

On screens 768px and below:

```
Sidebar: position fixed, translateX(-100%) by default
         transforms to translateX(0) when .open is added

Backdrop: .sidebar-backdrop, fixed overlay
          display: block + opacity: 1 when .show is added
```

The sidebar closes automatically when a note is selected or created via `closeSidebar()`, providing the expected mobile UX of returning to the editor after selection.

---

## 13. Security Model

| Threat | Mitigation |
|--------|-----------|
| Password exposure | SHA-256 hash in source; brute-forceable but acceptable for single personal-use app |
| XSS via note content | `escHtml()` applied to all user strings before `innerHTML` write |
| Session hijacking | Not applicable; no network communication, no cookies |
| Data exfiltration | Not applicable; data never leaves the browser |
| Unauthorized access | `sessionStorage` flag cleared on tab close; `checkAuth()` on every page load |

The security model is intentionally minimal and appropriate for a personal, local-use tool. It is not designed for multi-user or networked deployment without significant hardening.
