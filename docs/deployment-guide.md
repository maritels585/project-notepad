# Deployment Guide
# Prompt Vault Notepad

**Last Updated:** 2026-03-28

---

## Overview

Prompt Vault is a single static HTML file with no build step required. Deployment consists of placing `index.html` somewhere a browser can access it.

---

## Option 1: Local File (Simplest)

Open `index.html` directly in any modern browser:

- Double-click `index.html` in your file explorer, or
- Drag and drop it onto a browser window, or
- Open it via `File > Open` in the browser

**Limitations when using `file://` protocol:**
- `navigator.clipboard.writeText` (copy to clipboard) may be blocked by some browsers. Use Chrome or Edge for best compatibility on local files.
- `crypto.subtle` (password hashing) requires a secure context in some browsers. Chrome allows it on `file://`; Firefox may block it.

If clipboard or hashing fails on `file://`, use Option 2 instead.

---

## Option 2: Local HTTP Server (Recommended for Development)

Serving the file over HTTP eliminates all `file://` protocol restrictions.

**Using Python (built-in):**
```bash
# Navigate to the project directory
cd "e:/VIBE CODE/Notepad"

# Python 3
python -m http.server 8080

# Then open: http://localhost:8080
```

**Using Node.js (npx, no install required):**
```bash
cd "e:/VIBE CODE/Notepad"
npx serve .
# Then open the URL shown in the terminal
```

**Using VS Code Live Server extension:**
1. Install the "Live Server" extension in VS Code
2. Right-click `index.html` in the explorer
3. Select "Open with Live Server"
4. Browser opens automatically at `http://127.0.0.1:5500`

---

## Option 3: GitHub Pages (Free, Public)

1. Create a GitHub repository
2. Add `index.html` to the repository root
3. Go to **Settings > Pages**
4. Set Source to `Deploy from a branch`, select `main` branch, folder `/`
5. Save — the app is available at `https://{username}.github.io/{repo-name}/`

**Note:** GitHub Pages is public. Anyone with the URL can reach the login screen. The hardcoded credentials are visible in the page source. Do not store sensitive prompts if the repository is public.

For private use, make the repository private (requires a paid GitHub plan for private Pages).

---

## Option 4: Netlify Drop (Instant, No Account Required)

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag and drop the `index.html` file (or the containing folder)
3. Netlify assigns a random URL (e.g., `https://sparkling-fox-12ab34.netlify.app`)

The URL is publicly accessible. The same security note from Option 3 applies.

To use a custom domain, create a free Netlify account and configure DNS settings.

---

## Option 5: Cloudflare Pages

1. Create a Cloudflare account
2. Go to **Workers & Pages > Pages > Create a project**
3. Choose **Direct upload**
4. Upload the `index.html` file
5. Deploy — the app is available at `https://{project-name}.pages.dev`

---

## Option 6: Any Web Server

Copy `index.html` to the web root of any HTTP server:

- **Nginx:** Place in `/usr/share/nginx/html/` or your configured `root` directory
- **Apache:** Place in `/var/www/html/` or your `DocumentRoot`
- **IIS:** Place in `C:\inetpub\wwwroot\`

No server-side processing, no configuration, no dependencies. Any static file server works.

---

## HTTPS Requirement

Serving over HTTPS is recommended to ensure:
- `crypto.subtle` (password hashing) works in all browsers
- `navigator.clipboard.writeText` (copy to clipboard) works in all browsers

All options above except Option 1 (local file) serve over HTTPS or localhost, which satisfies the secure context requirement.

---

## Data Persistence Notes

All note data is stored in the **browser's `localStorage`** for the specific origin (domain + port) where the app is served.

- Data from `file://` is stored separately from `http://localhost:8080`
- Data from `localhost:8080` is stored separately from `localhost:3000`
- Data from `https://my-vault.netlify.app` is stored at that origin

If you switch deployment methods, use the **Export** button to download a JSON backup first, then **Import** it after opening the app at the new URL.

---

## Updating the Application

To update to a new version of `index.html`:

1. Export your notes via the Export button (saves a dated `.json` file)
2. Replace the old `index.html` with the new version
3. Open the app and log in
4. Import your backup JSON file if the notes did not carry over

Notes will carry over automatically if you replace the file at the same origin (same domain and port), since `localStorage` persists independently of the HTML file.

---

## Browser Compatibility

| Browser | Minimum Version | Notes |
|---------|----------------|-------|
| Chrome / Edge | 90+ | Full support including `file://` |
| Firefox | 90+ | Clipboard and crypto may need HTTPS |
| Safari | 15+ | Full support on HTTPS |
| Mobile Chrome | 90+ | Full support |
| Mobile Safari | 15+ | Full support on HTTPS |

The application does not support Internet Explorer.
