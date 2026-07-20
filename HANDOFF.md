# NMCT Demos — Handoff

Continue this project in the Claude Code extension (VS Code) or any local
editor. This captures the full state so you (or a fresh Claude session) can pick
up without re-discovering anything.

_Last updated: 2026-07-20._

---

## 1. What this project is

Two interactive homepage **design comps** for the **Native Medicare Charitable
Trust (NMCT)** — a Coimbatore charitable trust (est. 1988). Prepared by Quantzi
for client review. Both are live and shared with the client.

- **Concept A (cinematic):** `demo-1/` — motion-led, editorial homepage.
- **Concept B (structured):** `demo-2/` — clean, product-led homepage with a
  live donation widget.

**Live:** https://nmct.demo.quantzi.in/ → `/demo-1/` and `/demo-2/`
(landing page lets you pick either).

---

## 2. Repo & branch

- **Repo:** `kamalraj-quantzi/nmct`
- **Working branch:** `claude/work-assistance-mvhjzj` — **all work is here, NOT
  yet merged to `main`.** Keep committing here; open a PR to `main` when ready.

### First 5 minutes in VS Code
```bash
git fetch origin
git checkout claude/work-assistance-mvhjzj
git pull origin claude/work-assistance-mvhjzj

# Preview locally (vendored React means it works fully offline):
cd deploy/demo.quantzi.in
python3 -m http.server 8080
# open http://localhost:8080/  (landing) or /demo-1/  or /demo-2/
```
In VS Code you can also just use the **Live Server** extension on
`deploy/demo.quantzi.in/index.html`.

---

## 3. Directory map

```
README.md                     Project overview + Demo A/B comparison
HANDOFF.md                    ← this file
.github/workflows/
  deploy-demo.yml             FTP auto-deploy workflow (NOT active — needs secrets)
NCMT demo/                    Original design exports (x-dc source comps)
  demo 1/NMCT Homepage.dc.html
  demo 2/NMCT Home.dc.html
  demo {1,2}/support.js, image-slot.js
  demo {1,2}/uploads/         Reference PNGs + internal proposal PDFs (NOT deployed)
deploy/
  DEPLOY.md                   Manual cPanel upload steps
  AUTO-DEPLOY.md              GitHub→GoDaddy FTP setup steps
  demo.quantzi.in/            ← THE DEPLOYABLE SITE (this is what's live)
    index.html                Landing chooser page (plain static, no runtime)
    demo-1/  index.html, support.js, image-slot.js
    demo-2/  index.html, support.js, image-slot.js
    vendor/  react, react-dom, babel (UMD, served locally)
```

---

## 4. How the demos work (important tech notes)

- The `.dc.html` / `index.html` pages use the **DesignCraft `x-dc` runtime**
  (`support.js`) — a small React framework. `support.js` **self-boots**: it
  loads React, ReactDOM, and Babel, then mounts the `<x-dc>` markup on
  `DOMContentLoaded`. No build step.
- By default `support.js` fetches React from **unpkg.com**. For a self-contained
  deploy (no third-party CDN dependency), React/ReactDOM/Babel are **vendored**
  into `deploy/demo.quantzi.in/vendor/`, and each demo's `index.html` has this
  block in `<head>` (before `support.js`) redirecting the runtime to the local
  copies:
  ```html
  <script>
  window.__resources = {
    "https://unpkg.com/react@18.3.1/umd/react.production.min.js": "../vendor/react.production.min.js",
    "https://unpkg.com/react-dom@18.3.1/umd/react-dom.production.min.js": "../vendor/react-dom.production.min.js",
    "https://unpkg.com/@babel/standalone@7.29.0/babel.min.js": "../vendor/babel.min.js"
  };
  </script>
  ```
  `../vendor/` resolves to the site-root `vendor/` folder. Keep this block when
  editing the deploy copies.
- **Styling** is inline (`style="..."`) plus a small `<style>` block in the
  `<helmet>`; interactivity/data lives in the trailing
  `<script type="text/x-dc">` component class. `style-hover`, `style-focus`,
  `sc-for`, `sc-if`, `{{ }}` are x-dc conventions.
- **Placeholder content is intentional:** impact numbers, 12A/80G/FCRA
  registration numbers, partner logos, and photos (loaded from `nmctngo.org`)
  are stand-ins to be replaced with NMCT's verified assets before launch.

### ⚠️ The one duplication wart
Edits were applied to **both**:
1. `NCMT demo/demo 1/NMCT Homepage.dc.html` (clean source, **no** `__resources`)
2. `deploy/demo.quantzi.in/demo-1/index.html` (deployed, **has** `__resources`)

**Recommended workflow going forward:** edit the **`deploy/demo.quantzi.in/…`**
copy (that's what ships), preview it, then optionally mirror the change back to
`NCMT demo/…` to keep the source clean. Demo 2 has never been changed, so its two
copies are identical except the `__resources` block.

---

## 5. Deploying an update

The site is on **GoDaddy cPanel shared hosting**. Auto-deploy is written but
**not active** (secrets never added), so deploys are currently **manual**:

1. Edit files under `deploy/demo.quantzi.in/…` and preview locally.
2. Zip the changed demo's folder contents (top-level, no wrapper folder):
   ```bash
   cd deploy/demo.quantzi.in/demo-1
   zip -r ../../nmct-demo-1-update.zip index.html support.js image-slot.js
   ```
3. cPanel → **File Manager** → `public_html/nmct.demo.quantzi.in/demo-1` →
   **Upload** the zip → **Extract** → **Overwrite** → delete the zip.
4. **Set file permissions to 644** (see gotcha below), then hard-refresh.

To switch to **automatic** deploys instead: follow `deploy/AUTO-DEPLOY.md` (add
4 `FTP_*` secrets in GitHub repo settings + merge the workflow to `main`).

### Hosting facts (hard-won — see gotchas)
- **Server IP:** `68.178.145.241` (GoDaddy)
- **Document root:** `/public_html/nmct.demo.quantzi.in/` (the subdomain's files
  sit directly here: `index.html`, `demo-1/`, `demo-2/`, `vendor/`)
- **DNS is managed at the GoDaddy _domain_ dashboard** (dcc.godaddy.com →
  quantzi.in → DNS), **NOT** in cPanel Zone Editor. The A record
  `nmct.demo → 68.178.145.241` lives there.

### 🔥 Gotchas already hit (don't repeat)
1. **File permissions:** GoDaddy Apache refuses to serve **world-writable
   (0777)** files → returns a generic 404. **All files must be 644, dirs 755.**
   The zip-extract usually sets this right; if a page 404s after upload, check
   perms first. Bulk fix via cPanel Terminal:
   ```bash
   find ~/public_html/nmct.demo.quantzi.in -type f -exec chmod 644 {} \;
   find ~/public_html/nmct.demo.quantzi.in -type d -exec chmod 755 {} \;
   ```
2. **DNS lives at GoDaddy domain DNS**, not cPanel. Creating a cPanel subdomain
   does NOT create the public DNS record — you add an A record at
   dcc.godaddy.com. (This cost a long debugging loop.)
3. **`.co` vs `.in`:** the account's primary domain is `quantzi.co`; the demos
   are on `quantzi.in`. Easy to create a subdomain on the wrong TLD.

---

## 6. What's been done

- Packaged both comps into a self-contained static site (`deploy/`), vendored
  React (no CDN dependency), added landing chooser page.
- Deployed live to `https://nmct.demo.quantzi.in/` (demo-1 + demo-2).
- Wrote `README.md`, `DEPLOY.md`, `AUTO-DEPLOY.md`, and the GH Actions workflow.
- **Demo 1 frontend fixes** (all live-verified):
  - Fixed hero heading overlapping the top nav.
  - Reserved header space + tightened hero spacing so the hero buttons no longer
    bleed into the next section.
  - Removed the animated mouse scroll-cue icon.
  - Removed the entire "1988 → Today" timeline section.
  - Mobile (`≤820px`): hide the cramped utility row + desktop nav links.

## 7. Open / next steps

- [ ] **Demo 2** has had **no** polish pass yet — same responsive review could
      be applied (`deploy/demo.quantzi.in/demo-2/index.html`).
- [ ] Replace **placeholder content** (impact numbers, registration numbers,
      partner logos, real photography) — needs assets from NMCT.
- [ ] Optional: real **mobile menu** (hamburger). Currently nav links are just
      hidden below 820px, not replaced with a menu.
- [ ] **Merge to `main`** via PR when the client signs off.
- [ ] Optional: activate **auto-deploy** (add FTP secrets, per `AUTO-DEPLOY.md`).

## 8. How to verify a change

Serve `deploy/demo.quantzi.in/` locally and open the demo in a browser (works
offline thanks to vendored React). Check desktop **and** a narrow (~390px)
width. Photos/logos will be blank offline (they load from `nmctngo.org`) — that
is expected; check layout, spacing, and that React boots (no console errors).
