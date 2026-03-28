# Project Roadmap
# Prompt Vault Notepad

**Last Updated:** 2026-03-28

---

## Shipped Features (v1.0.0)

All five implementation phases are complete.

### Phase 1 — Authentication
- [x] Loading screen with spinner and fade-out transition
- [x] Login screen with card layout, gradient accent bar, logo
- [x] SHA-256 password hashing via Web Crypto API
- [x] sessionStorage session flag (cleared on tab close)
- [x] Shake animation on failed login
- [x] Logout button with session clear

### Phase 2 — Design System
- [x] CSS custom property token system (`--c-*` prefix)
- [x] Light theme (ivory/gray palette, blue accent `#4F6EF7`)
- [x] Dark theme (dark navy palette, blue accent `#6C8AFF`)
- [x] Theme toggle button with sun/moon icon swap
- [x] Theme preference persisted in `localStorage`
- [x] No flash of wrong theme on page load
- [x] Radial gradient background orbs (decorative)
- [x] Subtle 80px grid overlay

### Phase 3 — Core Application Structure
- [x] Two-panel layout: fixed sidebar + fluid main editor
- [x] Note list with title, 2-line preview, relative timestamp, tag badge
- [x] Note list sorted by `updatedAt` descending
- [x] Tag filter bar (All, Prompt, ACC, Khác, System)
- [x] Search input with 300ms debounce
- [x] Note count label in toolbar
- [x] Responsive layout with 3 breakpoints (1024px, 768px, 480px)
- [x] Mobile sidebar overlay with hamburger toggle
- [x] Sidebar auto-close on note select or create (mobile)
- [x] Empty state with shortcut hints

### Phase 4 — Note Management
- [x] Create note with UUID (prepended to list)
- [x] Select and open note in editor
- [x] Title input, tag select, last-updated meta
- [x] Content textarea (Times New Roman, 1.05rem, 1.9 line-height)
- [x] Auto-save with 500ms debounce
- [x] Manual save via `Ctrl+S`
- [x] ACC mode: line-numbered editor (JetBrains Mono, min 50 lines, auto-expand)
- [x] ACC line-number scroll sync
- [x] Tag change triggers immediate editor re-render
- [x] Duplicate note (appends " (bản sao)")
- [x] Delete note with confirmation modal
- [x] Character count in status bar
- [x] Full keyboard shortcut set (`Ctrl+N/S/K/D`, `Delete`, `Escape`)

### Phase 5 — Export, Import, and Polish
- [x] Copy note content to clipboard (`Ctrl+Shift+C` / clipboard button)
- [x] JSON export — all notes to dated `.json` file
- [x] JSON import — file validation + replace-all with confirmation modal
- [x] Toast notification system (success / error / info)
- [x] Toast progress bar animation
- [x] Toast auto-dismiss after 2.8 seconds
- [x] Delete and import confirmation modals
- [x] Seed data for first run (3 example prompts)
- [x] `localStorage` quota error handling

---

## Known Limitations

These are intentional design constraints, not bugs:

| Limitation | Rationale |
|------------|-----------|
| Single user only | Personal tool; multi-user requires a backend |
| Import replaces all data | Simpler UX; merge strategies add complexity |
| No note revision history | Scope control; would require IndexedDB or significant storage |
| No Markdown rendering | Intentional — plain text is more portable for prompt use |
| Clipboard API may fail on `file://` | Browser security restriction; use localhost or HTTPS for reliable clipboard |
| 5MB storage cap | `localStorage` browser limit; export regularly for large collections |

---

## Potential Future Enhancements

These are ideas not yet planned for implementation.

### High Priority

| Feature | Description |
|---------|-------------|
| Merge import | Import JSON and merge into existing notes by ID, rather than replacing all |
| Note ordering | Manual drag-to-reorder or pin-to-top functionality |
| Note folders | Group notes into named folders/collections beyond the 4 tag categories |
| Multi-select delete | Select and delete multiple notes at once |

### Medium Priority

| Feature | Description |
|---------|-------------|
| Additional tags | Allow custom user-defined tags |
| Note templates | Quick-create from a set of starter templates |
| Word count | Display word count alongside character count in status bar |
| Find and replace | In-editor find/replace for large prompts |
| Keyboard shortcut overlay | Press `?` to show all available shortcuts |

### Low Priority / Exploratory

| Feature | Description |
|---------|-------------|
| Optional cloud sync | Export/import via a configured endpoint (would require minimal backend) |
| PWA (offline app) | Add `manifest.json` and service worker for installable app experience |
| Note encryption | Encrypt note content at rest in `localStorage` |
| Tag color customization | Let users pick colors for each tag |
| Multiple themes | Additional preset themes beyond light/dark |
| Print view | Clean print stylesheet for exporting prompts to PDF |

---

## Migration Considerations

If the project ever grows beyond a single HTML file:

1. Extract CSS to `styles.css` — the token system is already structured for this
2. Extract JavaScript to `app.js` — the IIFE structure makes this straightforward
3. Replace localStorage with IndexedDB for larger datasets (using a library like Dexie.js)
4. Add a simple Express or Hono backend for multi-user support and cloud sync
5. Consider a build tool (Vite) only if dependencies grow beyond Google Fonts
