# Deploying the NMCT demos to demo.quantzi.in

This folder (`demo.quantzi.in/`) is a **self-contained static site**. There is
no build step and no server-side code — just upload the files to the web root
of the `demo.quantzi.in` subdomain.

```
demo.quantzi.in/
├── index.html          → landing page (choose Concept A or B)
├── demo-1/             → Concept A (cinematic homepage)
│   └── index.html, support.js, image-slot.js
├── demo-2/             → Concept B (structured homepage)
│   └── index.html, support.js, image-slot.js
└── vendor/             → React, ReactDOM, Babel (served locally, no CDN)
```

Resulting URLs:

- `https://demo.quantzi.in/`         → landing chooser
- `https://demo.quantzi.in/demo-1/`  → Concept A
- `https://demo.quantzi.in/demo-2/`  → Concept B

## Step 1 — Create the subdomain in GoDaddy

If `demo.quantzi.in` doesn't exist yet:

1. Log in to GoDaddy → **My Products** → your hosting plan → **cPanel Admin**.
2. In cPanel, open **Domains** (or **Subdomains**).
3. Create subdomain **`demo`** under `quantzi.in`. Note the **document root**
   cPanel assigns it (usually `public_html/demo` or `demo.quantzi.in`).

> If you use GoDaddy **Website Builder** rather than cPanel hosting, static
> file upload isn't supported — tell me and I'll suggest an alternative
> (e.g. a free static host pointed at the subdomain via DNS).

## Step 2 — Upload the files

**Option A — cPanel File Manager (easiest, no credentials shared):**

1. cPanel → **File Manager** → open the subdomain's document root from Step 1.
2. Click **Upload** and upload `demo.quantzi.in.zip` (see below), then
   **Extract** it there. Make sure `index.html` sits directly in the document
   root — not inside a nested `demo.quantzi.in/` folder. Move files up one
   level if the extract created an extra folder.

**Option B — FTP/SFTP (FileZilla):**

1. Get FTP credentials from cPanel → **FTP Accounts**.
2. Connect and drag the **contents** of `demo.quantzi.in/` into the
   subdomain's document root.

## Step 3 — Verify

Visit `https://demo.quantzi.in/`. You should see the landing page; both
concept links should open and animate. Hard-refresh (Ctrl/Cmd-Shift-R) if you
re-upload.

## Notes

- **Requirements:** any standard static web host. No PHP, Node, or database.
- **External assets:** placeholder photos and the NMCT logo currently load from
  `www.nmctngo.org`; partner "logos" are text for now. Fonts load from Google
  Fonts. All are replaced with final assets before the real site build.
- **Search engines:** the landing page carries `noindex,nofollow` so the demo
  won't show up in Google while it's under review.
- **HTTPS:** make sure the subdomain has an SSL certificate (GoDaddy/cPanel
  **AutoSSL** usually issues one automatically within a few minutes).
