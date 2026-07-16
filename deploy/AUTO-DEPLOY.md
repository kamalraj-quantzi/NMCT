# Auto-deploy: GitHub → GoDaddy (demo.quantzi.in)

Every push to `main` that changes `deploy/demo.quantzi.in/**` uploads the site
to the GoDaddy subdomain automatically via FTP. You can also run it by hand from
the **Actions** tab at any time.

The workflow lives at `.github/workflows/deploy-demo.yml`. It uses the
well-maintained [`SamKirkland/FTP-Deploy-Action`], pinned to `v4.3.5`, and does
an **incremental sync** (only changed files are uploaded).

## One-time setup

### 1. Get FTP details from GoDaddy

1. GoDaddy → **My Products** → hosting → **cPanel Admin**.
2. cPanel → **Subdomains**: create `demo` under `quantzi.in` if it doesn't
   exist, and note its **Document Root** (e.g. `public_html/demo`).
3. cPanel → **FTP Accounts**: use an existing account or create one scoped to
   that document root. Note the **username**, **password**, and **FTP server /
   host** (shown as something like `ftp.quantzi.in` or a server hostname).

### 2. Add the secrets in GitHub

Repo → **Settings** → **Secrets and variables** → **Actions** → **New
repository secret**. Add these four (I never see them):

| Secret name      | Value / example |
|------------------|-----------------|
| `FTP_SERVER`     | `ftp.quantzi.in` (host only — no `ftp://`, no path) |
| `FTP_USERNAME`   | your FTP account username |
| `FTP_PASSWORD`   | that account's password |
| `FTP_SERVER_DIR` | document root **with a trailing slash**, e.g. `/public_html/demo/` |

> **`FTP_SERVER_DIR` must match the subdomain's document root and end with `/`.**
> If it's wrong, files land in the wrong folder and the site won't appear.

### 3. Run the first deploy

Once the workflow file is on the **default branch (`main`)**:

- It runs automatically on the next push that touches the demo bundle, **or**
- Go to **Actions → Deploy demo.quantzi.in → Run workflow** to trigger it now.

> The workflow file currently lives on the feature branch. To enable automatic
> push-triggered deploys, merge it into `main`. To test before merging, use
> **Run workflow** from the Actions tab and pick this branch.

### 4. Verify

Watch the run in the **Actions** tab. On success, open `https://demo.quantzi.in/`.
Make sure the subdomain has SSL (cPanel **AutoSSL** usually handles this).

## Notes & troubleshooting

- **FTPS vs FTP:** the workflow uses `protocol: ftps` (FTP over TLS). If GoDaddy
  rejects it, change that line in `deploy-demo.yml` to `protocol: ftp`.
- **`dangerous-clean-slate` is off**, so the deploy won't delete other files in a
  shared document root. If `demo` has its own dedicated root and you want it kept
  perfectly in sync (deletions included), you can set it to `true`.
- **Sync state:** the action keeps a `.ftp-deploy-sync-state.json` file on the
  server to track what's already uploaded — leave it in place.
- **Manual upload** is still available as a fallback — see `DEPLOY.md`.

[`SamKirkland/FTP-Deploy-Action`]: https://github.com/SamKirkland/FTP-Deploy-Action
