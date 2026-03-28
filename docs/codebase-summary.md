# Codebase Summary
# Prompt Vault Notepad

**Last Updated:** 2026-03-28T12:00:00+07:00
**Generated From:** `e:/VIBE CODE/Notepad/index.html` (1,628 lines)

---

## File Structure

```
e:/VIBE CODE/Notepad/
├── index.html          # Entire application — ~1,628 lines
├── plans/
│   └── 20260328-1200-prompt-vault-notepad/
│       └── plan.md     # Original implementation plan
└── docs/
    ├── codebase-summary.md       (this file)
    ├── project-overview-pdr.md
    ├── code-standards.md
    ├── system-architecture.md
    ├── project-roadmap.md
    ├── design-guidelines.md
    └── deployment-guide.md
```

There are no separate CSS, JavaScript, or asset files. All styles, markup, and logic are co-located in `index.html`.

---

## index.html Structure

```
index.html
├── <head>
│   ├── Meta tags (charset, viewport)
│   ├── Title: "⚡ Prompt Vault"
│   ├── Google Fonts links (Be Vietnam Pro, JetBrains Mono)
│   └── <style> block (~824 lines of CSS)
│       ├── Design tokens — light theme (:root / [data-theme="light"])
│       ├── Design tokens — dark theme ([data-theme="dark"])
│       ├── Global reset and base styles
│       ├── Loading screen
│       ├── Login screen and card
│       ├── App layout (sidebar + main)
│       ├── Sidebar components (header, search, tags-bar, note-list, create-btn)
│       ├── Main editor (toolbar, editor-area, editor-card, title-input, meta, textarea)
│       ├── Status bar
│       ├── Empty state
│       ├── Modal overlay and modal
│       ├── Toast notifications
│       ├── Responsive breakpoints (1024px, 768px, 480px)
│       ├── Slide-in animation for note items
│       └── Line-numbered editor (ACC mode)
├── <body>
│   ├── #loadingScreen        — Spinner shown on startup
│   ├── #loginScreen          — Login form (hidden after auth)
│   ├── #appContainer         — Main app (hidden before auth)
│   │   ├── <aside.sidebar>
│   │   │   ├── .sidebar-header (logo, theme toggle, logout, search)
│   │   │   ├── .tags-bar (filter buttons: Tất cả, Prompt, ACC, Khác, System)
│   │   │   ├── #noteList (dynamically rendered note cards)
│   │   │   └── .create-btn-wrap (#createBtn)
│   │   ├── .sidebar-backdrop (mobile overlay)
│   │   └── <main.main>
│   │       ├── .toolbar (menu toggle, note count, action buttons)
│   │       ├── #editorArea (empty state or editor-card, dynamically rendered)
│   │       └── #statusBar (save state, char count, shortcut hints)
│   ├── #deleteModal          — Confirmation modal for deletion
│   ├── #importModal          — Confirmation modal for import
│   ├── #toastContainer       — Stack of toast notifications
│   ├── #importFileInput      — Hidden <input type="file"> for import
│   └── <script>              — IIFE-wrapped JavaScript (~626 lines)
│       ├── Constants
│       ├── State variables
│       ├── DOM references
│       ├── Utilities (debounce, escHtml, formatDate, tagLabel)
│       ├── Toast system
│       ├── Theme management
│       ├── Auth (SHA-256 hash, session check, login, logout)
│       ├── Storage (loadNotes, saveNotes with seed data)
│       ├── Render (renderNoteList, renderEditor, renderEmptyState, updateCharCount)
│       ├── ACC mode (buildTextareaHtml, updateLineNumbers, syncLineScroll)
│       ├── Note CRUD (createNote, selectNote, updateActiveNote, duplicateNote, deleteNote)
│       ├── Clipboard (copyToClipboard)
│       ├── Export/Import
│       ├── Search and tag filter
│       ├── Mobile sidebar
│       ├── Event bindings
│       └── Init (checkAuth)
```

---

## Key Components

### Constants

```javascript
const STORAGE_KEY = 'pv_notes';   // localStorage key for all notes
const AUTH_KEY    = 'pv_auth';    // sessionStorage key for auth flag
const THEME_KEY   = 'pv_theme';   // localStorage key for theme preference
const VALID_USER  = 'vananhseon'; // hardcoded username
const VALID_HASH  = '88bc42ee...'; // SHA-256 of password 'anh123'
```

### State Variables

```javascript
let notes         = [];    // Array<Note> — current in-memory note store
let activeNoteId  = null;  // string | null — ID of the note open in the editor
let activeTag     = 'all'; // string — current tag filter selection
let searchQuery   = '';    // string — current search filter text
let pendingImportData = null; // Array<Note> | null — staged import payload
```

### Utility Functions

| Function | Signature | Description |
|----------|-----------|-------------|
| `debounce` | `(fn, ms) => fn` | Returns a debounced version of `fn` |
| `escHtml` | `(s: string) => string` | XSS-safe HTML escaping via DOM `textContent` trick |
| `formatDate` | `(ts: number) => string` | Relative timestamp in Vietnamese |
| `tagLabel` | `(tag: string) => string` | Maps internal tag key to display label |

### Authentication Functions

| Function | Description |
|----------|-------------|
| `hashPassword(pwd)` | Async; uses `crypto.subtle.digest('SHA-256', ...)` and returns hex string |
| `checkAuth()` | Entry point; waits 600ms, fades out loading screen, branches to `showApp` or `showLogin` |
| `handleLogin(e)` | Form submit handler; hashes password and compares to `VALID_HASH` |
| `handleLogout()` | Removes `pv_auth` from `sessionStorage` and calls `showLogin` |

### Storage Functions

| Function | Description |
|----------|-------------|
| `loadNotes()` | Reads `pv_notes` from `localStorage`; seeds 3 sample notes if empty |
| `saveNotes()` | Serializes `notes` array to `localStorage`; shows error toast on `QuotaExceededError` |

### Render Functions

| Function | Description |
|----------|-------------|
| `renderNoteList()` | Filters and sorts notes; writes HTML to `#noteList`; updates note count label |
| `renderEditor(note)` | Writes the full editor card HTML to `#editorArea`; attaches input event listeners |
| `renderEmptyState()` | Restores the empty placeholder to `#editorArea` |
| `buildTextareaHtml(note)` | Returns either a plain `<textarea>` or a lined-editor wrapper depending on `note.tag` |
| `updateLineNumbers()` | Recalculates and updates the `.line-numbers` div for ACC mode |
| `syncLineScroll()` | Syncs scroll position of the line-numbers gutter with the textarea |
| `updateCharCount()` | Updates the character count in the status bar |

### CRUD Functions

| Function | Description |
|----------|-------------|
| `createNote()` | Generates UUID, prepends new note to `notes`, saves, renders editor |
| `selectNote(id)` | Sets `activeNoteId`, renders editor for the selected note |
| `updateActiveNote()` | Reads form values, updates note in array, saves, re-renders list |
| `duplicateNote()` | Clones active note with new UUID and " (bản sao)" title suffix |
| `confirmDeleteNote()` | Shows `#deleteModal` |
| `deleteNote()` | Removes active note from array, saves, resets editor to empty state |

### Export / Import Functions

| Function | Description |
|----------|-------------|
| `exportNotes()` | Creates a Blob, triggers download named `prompt-vault-backup-YYYY-MM-DD.json` |
| `triggerImport()` | Clicks the hidden `#importFileInput` |
| `handleImportFile(e)` | Reads file, validates JSON structure, stores in `pendingImportData`, shows confirm modal |
| `confirmImport()` | Replaces `notes` with `pendingImportData`, saves, re-renders |

---

## Data Flow

```
User action
    │
    ├─ Login form submit
    │       └── hashPassword() → compare → sessionStorage.setItem('pv_auth') → showApp()
    │
    ├─ Create / edit note
    │       └── DOM input → debounce(500ms) → updateActiveNote() → saveNotes() → renderNoteList()
    │
    ├─ Search input
    │       └── input event → debounce(300ms) → searchQuery = val → renderNoteList()
    │
    ├─ Tag filter click
    │       └── handleTagFilter(tag) → activeTag = tag → renderNoteList()
    │
    ├─ Export
    │       └── exportNotes() → JSON.stringify(notes) → Blob download
    │
    └─ Import
            └── file input change → FileReader → validate → pendingImportData
                    └── confirm modal → notes = pendingImportData → saveNotes() → render
```

---

## External Dependencies

| Dependency | Source | Purpose |
|------------|--------|---------|
| Be Vietnam Pro | Google Fonts CDN | Primary UI font (weights 400–700) |
| JetBrains Mono | Google Fonts CDN | Monospace font for code/meta labels |
| Web Crypto API | Browser built-in | SHA-256 password hashing |
| Clipboard API | Browser built-in | Copy-to-clipboard |
| localStorage | Browser built-in | Note persistence and theme preference |
| sessionStorage | Browser built-in | Auth session flag |

No npm packages, no bundler, no framework, no runtime dependencies.

---

## Browser API Requirements

| API | Used For | Notes |
|-----|----------|-------|
| `crypto.subtle.digest` | Password hashing | Requires secure context (HTTPS or localhost) for some browsers; `file://` may work in Chrome |
| `crypto.randomUUID` | Note ID generation | Supported in all modern browsers |
| `navigator.clipboard.writeText` | Copy to clipboard | Requires user gesture; may require HTTPS |
| `localStorage` | Note and theme persistence | Available in all modern browsers |
| `sessionStorage` | Auth session | Available in all modern browsers |
| `FileReader` | JSON import | Available in all modern browsers |
| `URL.createObjectURL` | JSON export download | Available in all modern browsers |
