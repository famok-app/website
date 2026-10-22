# famok.app

The company website of FAMOK LLC, served at https://famok.app by Cloudflare Pages.

## What it is

Four static pages (Home, Contact, Privacy, Terms) and a 404 page. Plain HTML and one style sheet:
no build step, no scripts, no web fonts, no analytics, nothing fetched from anyone but famok.app.

The look is a neutral placeholder. It will be replaced when the product's brand is chosen.

## Layout

| Path | What |
|---|---|
| `site/` | Everything that is published. Nothing outside it is served. |
| `site/_headers` | Response headers for Cloudflare Pages, including the Content Security Policy that enforces the privacy page's promises in the browser. |
| `.githooks/` | Commit guard: refuses token shapes, private keys and names on a local deny list. |

## Rules for changes

- Nothing loaded from another origin: no CDN, no font service, no embed, no analytics snippet. The
  Content Security Policy in `site/_headers` blocks them anyway; keep it that way.
- If the privacy page's promises would stop being true, change the page in the same commit.
- Every page carries the same header and footer; a change to one is a change to all five.
- Wrap email addresses in `<!--email_off-->` ... `<!--/email_off-->` so Cloudflare never injects
  its email-obfuscation script.

## Preview locally

```sh
python -m http.server 8080 --directory site
```

Then open http://localhost:8080. The `_headers` file only takes effect on Cloudflare.

## Deploy

Cloudflare Pages, connected to this repository: production branch `main`, no build command, build
output directory `site`. Every push to `main` deploys.

## Commit guard

Enable once per clone:

```sh
git config core.hooksPath .githooks
```
