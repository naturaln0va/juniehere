# isjuniehereyet.com

A one-page site that answers the only question that matters: **Is Junie here yet?**

## Flipping it to YES 🎉

Open `index.html` and find this block near the top of `<body>`:

```js
const JUNIE_IS_HERE = false;
const BIRTH_DETAIL = ""; // e.g. "Born August 14, 2026 · 7 lb 4 oz"
```

1. Change `false` to `true`.
2. Optionally fill in `BIRTH_DETAIL`.
3. Commit and push — the site updates in about a minute.

You can do this from your phone at the hospital: open the repo on github.com (or the GitHub mobile app), tap the pencil icon on `index.html`, edit the one line, and commit.

**Preview the YES state** without changing anything by visiting
`isjuniehereyet.com/?preview=yes`.

## Analytics (PostHog)

The site counts page views and nothing else — no autocapture, no session
recording, no person profiles. The config lives in the `<head>` of
`index.html`. The `POSTHOG_KEY` there is the project's public **Project API
key**, which is safe to commit — it can only write events, not read them.

Visits show up in PostHog under **Web analytics**, or as `$pageview` events
in the activity feed.

### The reverse proxy

Requests go to `n.isjuniehereyet.com` rather than `posthog.com` — a PostHog
**managed reverse proxy**, which stops ad blockers from filtering out the
visit counts. It serves both the library (`/static/array.js`) and the event
endpoint from our own domain.

That subdomain is a DNS `CNAME` pointing at PostHog's proxy
(`…cf-prod-us-proxy.proxyhog.com`), alongside the GitHub Pages records
below. **Don't delete it** — if it stops resolving, analytics quietly stop.
The site itself keeps working either way; the loader is fire-and-forget.

If you ever drop the proxy, set `POSTHOG_HOST` back to
`https://us.i.posthog.com` and remove the `ui_host` line.

### If you paste a fresh snippet from PostHog

PostHog's setup snippets configure a full-fat install — `defaults: '<date>'`
turns on autocapture, pageleave, and web vitals. Pasting one over this block
would replace "count the visits" with "record everything." Take the hosts
out of their snippet, leave the opt-outs here alone.

## Hosting (GitHub Pages, free)

1. Create a GitHub repo (e.g. `juniehere`) and push these files:
   ```sh
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin git@github.com:YOUR_USERNAME/juniehere.git
   git push -u origin main
   ```
2. On GitHub: **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main`, folder `/ (root)` → Save.
3. Still under **Pages → Custom domain**, enter `isjuniehereyet.com` and save. (The `CNAME` file in this repo keeps that setting from being lost on future deploys.)
4. Check **Enforce HTTPS** once the certificate is issued (can take a few minutes to an hour).

## DNS setup (at your domain registrar)

Add these records for `isjuniehereyet.com`:

| Type  | Name | Value               |
|-------|------|---------------------|
| A     | @    | 185.199.108.153     |
| A     | @    | 185.199.109.153     |
| A     | @    | 185.199.110.153     |
| A     | @    | 185.199.111.153     |
| CNAME | www  | YOUR_USERNAME.github.io |

DNS changes usually propagate within minutes but can take up to a day.
