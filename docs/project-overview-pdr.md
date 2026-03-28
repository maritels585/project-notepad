# Project Overview & Product Development Requirements
# Prompt Vault Notepad

**Version:** 1.0.0
**Last Updated:** 2026-03-28
**Status:** Complete (all phases shipped)

---

## 1. Project Overview

Prompt Vault is a single-file Vietnamese-language web application for storing, organizing, and managing AI prompt collections. It is designed as a personal tool for a single authenticated user, requiring no backend infrastructure, no build tooling, and no external dependencies beyond Google Fonts.

The entire application ships as one `index.html` file that can be opened directly in a browser or hosted on any static file server.

### Goals

- Provide a fast, pleasant UI for writing and retrieving AI prompts
- Support offline-capable, zero-backend persistence via `localStorage`
- Enforce a minimal security boundary with a hashed login gate
- Support multiple prompt categories with specialized editing modes
- Allow data portability via JSON export and import

---

## 2. Scope

### In Scope

- Single authenticated user (credentials hardcoded and hashed)
- CRUD operations on notes (prompts)
- Tag-based categorization: Prompt, ACC, Khác (Other), System
- Full-text search across title and content
- Light/dark theme toggle with persisted preference
- JSON backup export and full-replace import
- Copy-to-clipboard for prompt content
- Duplicate note action
- ACC-mode line-numbered editor
- Keyboard shortcuts for primary actions
- Responsive layout supporting desktop, tablet, and mobile
- Vietnamese UI language throughout

### Out of Scope

- Multi-user support
- Cloud sync or remote storage
- Markdown rendering
- Rich text editing
- Server-side processing of any kind
- Note revision history

---

## 3. Functional Requirements

### FR-01: Authentication

| ID | Requirement |
|----|-------------|
| FR-01-1 | The application must show a loading screen on startup (min 600ms) before revealing the auth state |
| FR-01-2 | The application must display a login screen when no valid session exists |
| FR-01-3 | Login must validate the username against a hardcoded constant and the password against a SHA-256 hash using Web Crypto API |
| FR-01-4 | A valid session must be stored in `sessionStorage` (key: `pv_auth`) and cleared on logout or tab close |
| FR-01-5 | Failed login attempts must show an error message and animate the login card with a shake effect |
| FR-01-6 | The logout button must clear the session and return to the login screen |

### FR-02: Note Management

| ID | Requirement |
|----|-------------|
| FR-02-1 | Users must be able to create a new note with a unique UUID, empty title and content, and the current active tag |
| FR-02-2 | New notes must be prepended to the top of the notes array |
| FR-02-3 | Users must be able to select a note from the sidebar list to open it in the editor |
| FR-02-4 | Changes to title, content, or tag must auto-save to `localStorage` after a 500ms debounce |
| FR-02-5 | Users must be able to manually trigger a save via `Ctrl+S` |
| FR-02-6 | Users must be able to duplicate the active note (appends " (bản sao)" to the title) |
| FR-02-7 | Users must be able to delete the active note via a confirmation modal |
| FR-02-8 | The note list must display title, 2-line content preview, relative timestamp, and tag badge |
| FR-02-9 | The note list must be sorted by `updatedAt` descending |

### FR-03: Search and Filtering

| ID | Requirement |
|----|-------------|
| FR-03-1 | The search input must filter the note list in real time with a 300ms debounce |
| FR-03-2 | Search must match against both title and content (case-insensitive) |
| FR-03-3 | Tag filter buttons must filter the list to notes matching the selected tag |
| FR-03-4 | "All" filter must show all notes regardless of tag |
| FR-03-5 | The note count label must reflect the number of currently visible notes |

### FR-04: Editor

| ID | Requirement |
|----|-------------|
| FR-04-1 | The editor must display title input, tag selector, last-updated timestamp, and content textarea |
| FR-04-2 | The content textarea must use Times New Roman font at 1.05rem with 1.9 line height |
| FR-04-3 | When the note tag is ACC, the editor must render a line-numbered view with JetBrains Mono font |
| FR-04-4 | The ACC line-number gutter must show a minimum of 50 lines and expand automatically |
| FR-04-5 | The line-number gutter must scroll in sync with the textarea |
| FR-04-6 | Switching the tag dropdown from/to ACC must re-render the editor immediately |
| FR-04-7 | The status bar must show "Saved" state, character count, and keyboard shortcut hints |

### FR-05: Clipboard and Export/Import

| ID | Requirement |
|----|-------------|
| FR-05-1 | The Clipboard button must copy the active note's content to the system clipboard |
| FR-05-2 | Export must serialize all notes as a formatted JSON file, named with today's date |
| FR-05-3 | Import must read a JSON file, validate structure (array of objects with `id`, `title`, `content`), and prompt for confirmation before replacing all notes |
| FR-05-4 | Import validation failures must display an error toast |

### FR-06: Theme

| ID | Requirement |
|----|-------------|
| FR-06-1 | The application must support light and dark themes controlled by the `data-theme` attribute on `<html>` |
| FR-06-2 | The theme preference must be persisted in `localStorage` (key: `pv_theme`) |
| FR-06-3 | The theme must be applied immediately on page load before any visible render (no flash) |
| FR-06-4 | The theme toggle button icon must switch between sun (dark mode) and moon (light mode) |

### FR-07: Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+N` | Create new note |
| `Ctrl+S` | Save active note immediately |
| `Ctrl+K` | Focus search input |
| `Ctrl+D` | Duplicate active note |
| `Delete` | Open delete confirmation (when focus is not on an input) |
| `Escape` | Close any open modal or close mobile sidebar |

### FR-08: Notifications

| ID | Requirement |
|----|-------------|
| FR-08-1 | All user actions must produce a toast notification in the bottom-right corner |
| FR-08-2 | Toasts must auto-dismiss after 2.8 seconds |
| FR-08-3 | Toasts must display a progress bar that depletes over the dismiss duration |
| FR-08-4 | Toast types: `success` (green), `error` (red), `info` (blue/accent) |

### FR-09: Responsive Layout

| ID | Requirement |
|----|-------------|
| FR-09-1 | At widths above 768px the sidebar must be always visible |
| FR-09-2 | At 768px and below the sidebar must be hidden and accessible via a hamburger menu button |
| FR-09-3 | The mobile sidebar must overlay the content with a semi-transparent backdrop |
| FR-09-4 | Opening a note or creating a note on mobile must close the sidebar automatically |

---

## 4. Non-Functional Requirements

| Category | Requirement |
|----------|-------------|
| Performance | Page must be fully interactive in under 1.5 seconds on a modern browser with no network throttling |
| Storage | All note data must fit within the `localStorage` 5MB quota; a storage-full error must show a toast |
| Security | Passwords must never be stored in plain text; only a SHA-256 hex digest is present in source |
| Security | All user-generated strings rendered to HTML must use `textContent` assignment or DOM-safe escaping to prevent XSS |
| Compatibility | Must work in all modern browsers supporting ES2020, Web Crypto API, and `navigator.clipboard` |
| Accessibility | All interactive elements must have visible focus rings; SVG icons must have `title` attributes or be paired with visible labels |
| Portability | The entire application must function when the `index.html` file is opened directly from the filesystem (`file://` protocol), with the exception of clipboard API which requires HTTPS or localhost in some browsers |

---

## 5. Data Model

### Note Object

```json
{
  "id": "uuid-v4-string",
  "title": "string",
  "content": "string",
  "tag": "prompt | acc | other | system",
  "createdAt": 1711584000000,
  "updatedAt": 1711584000000
}
```

### Storage Keys

| Key | Storage | Type | Description |
|-----|---------|------|-------------|
| `pv_notes` | `localStorage` | JSON array of Note | All notes |
| `pv_auth` | `sessionStorage` | String `"true"` | Active session flag |
| `pv_theme` | `localStorage` | String `"light"` or `"dark"` | Theme preference |

---

## 6. Seed Data

On first run (empty `localStorage`), the application seeds three example notes:

1. "Prompt viết nội dung chuyên nghiệp" — tag: `prompt`
2. "Prompt debug code hiệu quả" — tag: `prompt` (created 1 hour ago)
3. "System Prompt cơ bản" — tag: `system` (created 2 hours ago)

---

## 7. Acceptance Criteria

- A user can log in with `vananhseon` / `anh123` and is rejected for any other credentials
- Creating, editing, and deleting notes persists across browser refreshes
- Closing the tab clears the session; reopening requires login
- Switching to dark mode persists across refreshes
- Exporting produces a valid JSON file that can be re-imported
- Importing an invalid JSON file shows an error toast without modifying existing data
- All keyboard shortcuts function as specified in FR-07
- The ACC editor shows line numbers starting at 1 that scroll in sync with the textarea
- On a viewport width of 375px the sidebar is hidden by default and toggled by the hamburger button
