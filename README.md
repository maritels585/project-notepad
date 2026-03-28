# Prompt Vault

A personal prompt notepad for storing, organizing, and managing AI prompt collections. Built as a single HTML file with no backend, no build tools, and no dependencies beyond Google Fonts.

Vietnamese UI. Light/dark themes. Works offline.

---

## Features

- **CRUD notes** — create, read, update, and delete prompts with auto-save (500ms debounce)
- **4 categories** — Prompt, ACC, Khác (Other), System — with color-coded tag badges
- **ACC mode** — line-numbered editor (JetBrains Mono, min 50 lines) for structured content
- **Search** — real-time full-text search across title and content (300ms debounce)
- **Tag filtering** — filter the sidebar list by category
- **Duplicate** — clone any note with one click
- **Copy to clipboard** — copy prompt content instantly
- **Export / Import** — JSON backup and full-replace import
- **Light / dark theme** — toggle with preference persisted in `localStorage`
- **Keyboard shortcuts** — `Ctrl+N`, `Ctrl+S`, `Ctrl+K`, `Ctrl+D`, `Delete`, `Escape`
- **Toast notifications** — feedback for every user action with a progress bar
- **Responsive** — desktop, tablet, and mobile with a slide-out sidebar overlay
- **No server required** — runs entirely in the browser from a single HTML file

---

## Setup

No installation, no dependencies, no build step.

**Option A — Open locally:**
```
Double-click index.html
```

**Option B — Local server (recommended for full clipboard support):**
```bash
cd "e:/VIBE CODE/Notepad"
python -m http.server 8080
# Open http://localhost:8080
```

**Option C — Static hosting:**
Upload `index.html` to GitHub Pages, Netlify Drop, Cloudflare Pages, or any web server.

See `docs/deployment-guide.md` for detailed instructions for each option.

---

## Login

```
Username: vananhseon
Password: anh123
```

The password is stored as a SHA-256 hash in the source. The session is held in `sessionStorage` and cleared when the tab is closed.

---

## Usage

### Creating a note
Click **Tạo Prompt Mới** in the sidebar footer or press `Ctrl+N`. The new note opens in the editor immediately.

### Writing
Type in the large textarea. Changes auto-save after 500ms. Press `Ctrl+S` to save immediately. The status bar shows "Đã lưu" and a live character count.

### Categories
Use the tag selector in the editor meta row to categorize a note. The ACC category switches the editor to a line-numbered view suited for structured content.

### Searching
Click the search box or press `Ctrl+K`. The list filters in real time as you type. Use the tag buttons at the top of the sidebar to filter by category.

### Exporting data
Click **Export** in the toolbar to download all notes as a dated JSON file:
```
prompt-vault-backup-2026-03-28.json
```

### Importing data
Click **Import**, select a previously exported JSON file, and confirm the replacement. Import validates the file structure before making any changes.

### Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+N` | Create new note |
| `Ctrl+S` | Save active note |
| `Ctrl+K` | Focus search |
| `Ctrl+D` | Duplicate active note |
| `Delete` | Open delete confirmation (when not in a text input) |
| `Escape` | Close modal / close mobile sidebar |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Application | Single `index.html` file |
| Markup | HTML5 |
| Styles | Vanilla CSS with custom property design tokens |
| Logic | Vanilla JavaScript (ES2020, IIFE, no framework) |
| Fonts | Google Fonts — Be Vietnam Pro + JetBrains Mono |
| Note content font | Times New Roman (system) |
| Auth | Web Crypto API (SHA-256) |
| Storage | `localStorage` (notes, theme) + `sessionStorage` (auth) |
| Build tools | None |
| Dependencies | None (besides Google Fonts CSS) |

---

## Data Storage

All notes are stored in `localStorage` under the key `pv_notes` as a JSON array. Data is scoped to the origin (domain + port) where the app is served.

If you change the URL (e.g., from `localhost:8080` to a new domain), export your data first and re-import it at the new URL.

---

## Documentation

| File | Description |
|------|-------------|
| `docs/project-overview-pdr.md` | Product requirements and acceptance criteria |
| `docs/codebase-summary.md` | File structure, components, functions, and data flow |
| `docs/code-standards.md` | Naming conventions, CSS architecture, JS patterns |
| `docs/system-architecture.md` | Auth flow, storage, rendering, modal and toast systems |
| `docs/project-roadmap.md` | Shipped features and future enhancement ideas |
| `docs/design-guidelines.md` | Design tokens, color system, typography, motion |
| `docs/deployment-guide.md` | Deployment options for local, hosted, and CDN use |

---

## Browser Support

Chrome 90+, Firefox 90+, Safari 15+, Edge 90+. No Internet Explorer.

HTTPS or `localhost` is recommended for full clipboard and Web Crypto API support.
