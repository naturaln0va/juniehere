# isjuniehereyet.com

A one-page site that answers the only question anyone is asking right now:

> **Is Junie here yet?**

It renders a single enormous word — **No** while we wait, **YES!** once she
arrives — and nothing else. No nav, no scroll, no cookie banner. You load it,
you get your answer, you close the tab.

## The idea

Every expectant parent knows the third-trimester group text: the same question,
from a dozen people, several times a day. This is the answer, at a URL, so
nobody has to ask and nobody has to reply.

That constraint drove everything:

- **One question, one word.** The answer is the entire page, sized to fill the
  screen. Readable across the room, on a phone, at a glance.
- **The waiting is part of it.** While the answer is "No" the page is
  deliberately drab — grey, muted, an hourglass ⏳ in the tab. When it flips,
  the whole thing warms up, turns green, throws confetti, and the favicon
  becomes a little angel 👼. The site should *feel* like the news.
- **Flipping it has to be trivial.** The switch is one boolean in one file, so
  it can be thrown one-handed from a hospital bed, from a phone, at 3am, by
  someone who has not slept. No build step, no deploy pipeline, no laptop.

## Peeking

You can see the celebration early — confetti and all — without spoiling
anything for anyone else:

**[isjuniehereyet.com/?preview=yes](https://isjuniehereyet.com/?preview=yes)**

That's a URL parameter, visible only to you. The real answer stays whatever
it actually is.

## Under the hood

One file. `index.html` holds the markup, the styles, and the script, with no
dependencies and no build — the whole site is a few kilobytes plus a couple of
images. It's served straight off GitHub Pages.

The only thing it phones home about is a page view count, so we know roughly
how many people are out there refreshing.

---

*Setup, deployment, DNS, and analytics details live in `CLAUDE.md`.*
