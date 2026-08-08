# CLAUDE.md

Operational context for `isjuniehereyet.com`. See `README.md` for what the site
is and why.

## Shape of the repo

A static single-page site. **No build, no dependencies, no package manager, no
tests, no CI.** There is nothing to install and nothing to run — open
`index.html` in a browser and that's the site.

```
index.html           the entire site: markup + CSS + JS in one file
CNAME                "isjuniehereyet.com" — GitHub Pages custom domain
.nojekyll            skips Jekyll processing on Pages
apple-touch-icon.png home-screen icon (static file; distinct from the favicon)
og-image.jpg         link-preview image (~535 KB)
```

Because there's no build, **edit `index.html` directly.** Don't introduce a
bundler, a framework, or a `package.json` for this — the no-tooling property is
what makes the site editable from a phone, which is the whole point.

## Deploying

GitHub Pages serves the root of `main` on `github.com/naturaln0va/juniehere`.

**Push to `main` = deploy.** There is no staging environment and no other
branch. Pages picks changes up in roughly 30 seconds; a hard refresh may be
needed to get past the CDN cache.

To verify a deploy actually landed, poll the live site rather than trusting the
push:

```sh
curl -sS "https://isjuniehereyet.com/?cb=$RANDOM" | grep <something-you-changed>
```

## The one thing that matters

Near the top of `<body>`:

```js
const JUNIE_IS_HERE = false;
const BIRTH_DETAIL = ""; // e.g. "Born August 14, 2026 · 7 lb 4 oz"
```

Flipping `JUNIE_IS_HERE` to `true` switches the page to the YES state: green
answer, warm background, confetti, angel favicon, and `BIRTH_DETAIL` (or a
fallback line) under the answer.

`?preview=yes` forces the YES state without touching the flag — useful for
checking changes to the celebration path.

**Keep this flip a one-line, one-file edit.** It's designed to be done from the
GitHub mobile app in a hospital, so anything that makes it require a second file
or a build step is a regression, however tidy it looks.

## Analytics

PostHog, deliberately configured to capture **page views and nothing else**.
The config is in the `<head>` of `index.html`.

- `POSTHOG_KEY` is the public **Project API key**. Write-only — safe in a public
  repo. Not a secret; don't try to hide it in an env var (there's no build to
  substitute one).
- `api_host` is `https://n.isjuniehereyet.com`, a PostHog **managed reverse
  proxy**, so ad blockers filtering `posthog.com` don't eat the counts. It
  serves both `/static/array.js` and the event endpoint.
- `ui_host` is `https://us.posthog.com` — required when proxying, so links
  inside the PostHog app point at the real app.
- The loader is fire-and-forget: appended async, no-ops if `POSTHOG_KEY` is
  cleared, and never blocks rendering the answer. **A broken proxy must not
  break the page.** Preserve that if you touch it.

Everything fancy is explicitly off: `autocapture`, `capture_pageleave`,
`capture_performance`, session recording, and person profiles
(`identified_only`, so visitors stay anonymous).

### If you're pasting a snippet from PostHog

PostHog's setup snippets configure a full install. In particular
`defaults: '<date>'` re-enables autocapture, pageleave, and web vitals, and
their snippets omit the opt-outs above. **Take only the hosts out of a new
snippet; leave the opt-out block alone.** Pasting one wholesale silently turns
"count the visits" into "record everything," which is not what this site wants.

Project-level settings (`capturePerformance.network_timing` etc.) in the
PostHog UI are overridden by the explicit client config here — an option set to
`false` in `posthog.init` wins over the remote config. So the UI showing a
toggle as enabled doesn't mean the site is sending it.

## DNS

At the registrar for `isjuniehereyet.com`:

| Type  | Name | Value                                      | Purpose |
|-------|------|--------------------------------------------|---------|
| A     | @    | 185.199.108.153 / .109.153 / .110.153 / .111.153 | GitHub Pages |
| CNAME | n    | `…cf-prod-us-proxy.proxyhog.com`           | PostHog analytics proxy |

Both are live and verified. The `n` record is load-bearing for analytics only —
if it stops resolving, visit counts silently stop and the site is otherwise
fine.

**Known gap:** there is no `www` record. `www.isjuniehereyet.com` does not
resolve at all — it fails rather than redirecting. Add a `CNAME www →
naturaln0va.github.io` if that's ever worth fixing.

The `CNAME` file in the repo root pins the Pages custom domain; deleting it
resets the domain on the next deploy.

## Conventions

- Vanilla everything. No frameworks, no CDN script tags beyond the analytics
  loader, no external fonts (system font stack).
- Theme colors live in CSS custom properties on `:root`, with a
  `prefers-color-scheme: dark` block overriding them. Add colors there, not
  inline.
- The favicon is generated at runtime as an inline SVG data URI (⏳ waiting,
  👼 arrived) — `apple-touch-icon.png` is the separate static one.
- Comments in `index.html` are written for a sleep-deprived human, not a
  compiler. Keep that register.
- Don't commit or push unless asked; a push publishes to the live site.
