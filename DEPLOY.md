# Connecting this repository to famok.app (Cloudflare Pages)

One-time setup. famok.app's DNS is already on Cloudflare, so the custom domain needs no DNS edits by
hand. Do this only after the site has been checked.

## 1. Create the Pages project

1. Cloudflare dashboard → **Workers & Pages** → **Create**. If the page offers Workers first, choose
   the **Pages** tab (or the "Looking to deploy Pages?" link), then **Import an existing Git
   repository**.
2. **Connect GitHub.** When GitHub asks where to install the Cloudflare Pages app, pick the
   **famok-app** organization and **Only select repositories → website**. Do not grant all
   repositories.
3. Pick **famok-app/website** → **Begin setup**.
4. Settings:
   - Project name: `famok` (gives the preview address `famok.pages.dev`; if taken, anything works)
   - Production branch: `main`
   - Framework preset: **None**
   - Build command: *(leave empty)*
   - Build output directory: `site`
5. **Save and Deploy.** The first deploy takes under a minute. Open the `*.pages.dev` address it
   shows and click through all four pages.

## 2. Attach famok.app

1. In the project: **Custom domains** → **Set up a custom domain** → `famok.app` → **Continue** →
   **Activate domain**. Cloudflare adds the DNS record itself because the zone is in the same
   account.
2. Wait for the status to read **Active** (usually a few minutes; the certificate is issued
   automatically).
3. Optional, later: `www.famok.app` redirecting to `famok.app` (Rules → Redirect Rules → template
   "Redirect from WWW to root"). Apple only needs `famok.app`.

## 3. Zone settings that keep the privacy page true

On the **famok.app** zone, check each of these. Cloudflare can add a script or a cookie to every page
at the edge, and the privacy page promises neither.

| Where | Setting | Should be |
|---|---|---|
| Scrape Shield (or Security → Settings) | Email Address Obfuscation | **Off** |
| Speed → Optimization | Rocket Loader | **Off** |
| Analytics & Logs → Web Analytics | famok.app site / automatic setup | **Not enabled** |
| Workers & Pages → famok → Metrics | Web Analytics | **Not enabled** |
| Security → Bots | Bot Fight Mode | **Off** (it can set a `__cf_bm` cookie and challenge visitors) |
| Zaraz | | **Not set up** |
| SSL/TLS → Edge Certificates | Always Use HTTPS | **On** |

The AI-crawler block set up with the DNS move can stay: it sets no cookie and adds no script.

## 4. Check it is live and clean

From any terminal:

```sh
curl -sI https://famok.app/ | grep -iE "^(HTTP|content-security-policy|set-cookie)"
curl -s https://famok.app/ | grep -ic "<script"
```

Expect: `HTTP/2 200`, a `content-security-policy` line, **no** `set-cookie` line, and `0` scripts.

Three checks curl cannot make (each one was missed once, 2026-10-22):

- **Look at it before pushing.** Screenshot every changed page at desktop width and at 390 px, and
  read the screenshots. A page that passes curl can still look careless.
- **Look at it as a returning visitor.** Cloudflare tells browsers to keep `style.css` for 4 hours;
  a stylesheet change without a new `?v=` number shows returning visitors the new pages unstyled.
  After pushing, open the site on a phone that has visited it before.
- **If any step regenerates a page, diff it against the last commit before pushing**, and confirm
  every approved line is still there word for word.
