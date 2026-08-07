# Your blog — how it all works

A friendly reference for everything you'll actually need. Nothing here
is urgent reading; dip in when you want to do a particular thing.

---

## The three commands you'll use

```bash
hugo server -D          # preview at http://localhost:1313 (-D shows drafts)
hugo                    # build the site into public/ (rarely needed by hand)
git add -A && git commit -m "new post" && git push    # publish
```

`hugo server` watches your files and reloads the browser the instant you
save. Leave it running in a terminal while you write. Stop it with `Ctrl+C`.

---

## Where everything lives

```
Blog/
├── hugo.toml                    ← site settings: title, menu, tags, etc.
├── content/                     ← YOUR WRITING lives here
│   ├── _index.md                    the homepage text
│   ├── about.md                     the about page
│   └── posts/                       blog posts
│       ├── hello-world.md               a simple post
│       └── images-video-and-tags/       a post with its own media
│           ├── index.md
│           └── sample-image.png
├── static/                      ← files copied to the site as-is
│   ├── CNAME                        your custom domain
│   └── css/
│       ├── custom.css               ← YOUR CSS TWEAKS go here
│       ├── syntax.css               code-block colours
│       └── palettes/
│           └── catppuccin-mocha.css ← your colour scheme
├── layouts/                     ← page templates (overrides the theme)
├── themes/risotto/              ← the theme — try not to edit this
└── public/                      ← the built site (auto-generated, ignore it)
```

The golden rule: **put your changes in `static/` and `layouts/`, not in
`themes/risotto/`.** Hugo always prefers your version of a file over the
theme's, so you get your customisations *and* the ability to update the
theme later without losing them.

---

## Writing a post

### Start one

```bash
hugo new posts/my-first-post.md
```

That creates the file with the front matter already filled in. Or just
copy an existing post — honestly that's often easier.

For a post **with its own images or video**, make it a folder instead:

```bash
hugo new posts/my-trip/index.md
```

then drop the pictures into `content/posts/my-trip/` next to `index.md`.
This is called a *page bundle*, and it's the tidier way to do it — the
post and its media stay together, and deleting the folder removes both.

### The front matter

The bit at the top between `+++` lines:

```toml
+++
title = "Dollywood Park Review"
date = 2026-04-28
draft = true
description = "Nine coasters, ranked."
tags = ["amusement-parks", "reviews"]
toc = false
+++
```

| Field | What it does |
| --- | --- |
| `title` | Heading and browser tab title |
| `date` | Sort order and the displayed date |
| `draft` | `true` = hidden from the live site. **Set to `false` to publish.** |
| `description` | One-line summary — sidebar, and link previews when shared |
| `tags` | Groups related posts (see below) |
| `toc` | `true` puts a table of contents in the sidebar |

> **The single most common gotcha:** a post that won't appear on the live
> site is almost always still `draft = true`.

---

## Images

Put the image in the post's folder, then:

```markdown
{{< img src="photo.jpg" alt="A cat asleep on a keyboard" caption="Beans, helping" >}}
```

- `alt` — describes the picture for screen-reader users, and shows if the
  image fails to load. Always worth writing.
- `caption` — optional, appears in italics underneath.

Big images are **automatically resized** to a sensible width, so you can
drop a full-size phone photo straight in without thinking about it.

### Size and alignment

```markdown
{{< img src="photo.jpg" alt="A cat" width="50%" >}}
{{< img src="photo.jpg" alt="A cat" width="300" align="center" >}}
{{< img src="photo.jpg" alt="A cat" width="20rem" align="right" >}}
```

- `width` — `"50%"`, `"300px"`, or just `"300"` (bare numbers are treated
  as pixels). Percentages are of the text column, not the whole screen.
- `align` — `"center"`, `"left"` or `"right"`. The caption follows the
  image. Without a width, `center` still centres the image.

On phones (under 600px wide) the width is ignored and images go full
width, because a 50% image on a phone screen is unreadably small.

### Plain markdown

Works too, and gets the same automatic resizing and lazy loading:

```markdown
![A cat asleep on a keyboard](photo.jpg)
```

But markdown has **no syntax for width or alignment** — it only
understands `![alt](src)`. That's a limitation of markdown itself, not
of Hugo. So whenever you want to size or position an image, use the
`img` shortcode above.

### If an image doesn't show up

The build now warns you by name:

```
WARN  img shortcode: couldn't find "photo.jpg" next to posts/Dollywood/index.md
```

Nine times out of ten it's capitalisation — `Photo.JPG` and `photo.jpg`
are different files as far as the web is concerned, even though your
computer may not care.

**For an image you reuse across posts**, put it in `static/images/` and
reference it with a leading slash — `src="/images/logo.png"`.

---

## Video

### Your own video files

```markdown
{{< video src="clip.mp4" caption="A short clip" >}}
```

Also accepts `poster="thumb.jpg"` (a thumbnail), `autoplay="true"` and
`loop="true"`. Autoplay is muted automatically, because browsers refuse
to autoplay sound.

⚠️ Video files are **big**. GitHub doesn't love large files in a repo, and
GitHub Pages has a 1 GB site limit. A few seconds is fine; anything
longer is happier on YouTube.

### YouTube / Vimeo

Just the ID from the URL — for `youtube.com/watch?v=dQw4w9WgXcQ` that's
`dQw4w9WgXcQ`:

```markdown
{{< youtube dQw4w9WgXcQ >}}
{{< vimeo 146022717 >}}
```

These are set to privacy-enhanced mode, so no tracking cookies unless a
reader presses play.

---

## How tags work

Add them to the front matter. That's the whole job:

```toml
tags = ["amusement-parks", "reviews"]
```

From that one line, Hugo automatically builds and maintains:

- **`/tags/`** — every tag you've used, with a count of posts
- **`/tags/reviews/`** — every post tagged `reviews`
- an **RSS feed** per tag, so someone can follow just one topic

You never create or delete those pages. They appear when you first use a
tag and vanish when you stop using it.

A few habits worth having:

- **Spelling must match exactly.** `sci-fi` and `scifi` become two
  separate tags. This is the one thing that trips people up.
- **Lowercase** is easiest, since tags end up in URLs.
- **Spaces are fine** — `"slow cooking"` becomes `/tags/slow-cooking/`.
- **A few per post** is plenty. Tags are for finding things later, not
  for describing everything.

### Categories

There's a second, identical system called categories:

```toml
categories = ["travel"]
```

Convention is a handful of broad categories plus lots of specific tags.
If that feels like overkill, delete the `category = "categories"` line
from the `[taxonomies]` block in `hugo.toml` and just use tags.

---

## Making it look how you want

### Your colours

`static/css/palettes/catppuccin-mocha.css` holds the official Catppuccin
Mocha palette. Every colour is available by name:

```css
var(--ctp-mauve)  var(--ctp-pink)   var(--ctp-teal)   var(--ctp-peach)
var(--ctp-base)   var(--ctp-mantle) var(--ctp-crust)  var(--ctp-text)
```

The accent colour is set near the top of `static/css/custom.css`:

```css
:root {
	--link:  var(--ctp-mauve);   /* links */
	--hover: var(--ctp-pink);    /* links on hover */
	--logo:  var(--ctp-mauve);   /* the [ blog ] $ block */
}
```

Change `--ctp-mauve` to `--ctp-teal` there and the whole site re-themes
instantly. Try it — it's quite satisfying.

### Everything else

**Put all your CSS in `static/css/custom.css`.** It loads last, so it
wins over the theme without you needing `!important` (except on code
blocks, where the theme uses it — there's already a note in the file).

To find out what to target: right-click anything on the page → Inspect.
The theme's own CSS is in `themes/risotto/static/css/` and is short and
readable if you want to see how something is built.

### Changing page structure

Copy the file you want to change from `themes/risotto/layouts/` into
`layouts/` (keeping the same subfolder path), then edit your copy. Hugo
uses yours instead. Already done for you:

| File | What was changed |
| --- | --- |
| `layouts/index.html` | homepage now lists recent posts |
| `layouts/_default/single.html` | posts show date + tags |
| `layouts/posts/list.html` | post list shows tags + pagination |
| `layouts/_default/taxonomy.html` | the `/tags/` page |
| `layouts/_default/term.html` | individual tag pages |
| `layouts/partials/head.html` | updated for current Hugo, better previews |
| `layouts/partials/tags.html` | the tag pills |
| `layouts/partials/lang.html` | emptied (language switcher not needed) |

---

## Publishing

### One-time setup

1. **Create the repo and push** (see the commands at the bottom).

2. **Turn on Pages:** repo → **Settings** → **Pages** → under
   "Build and deployment", set **Source** to **GitHub Actions**.

3. **Set the custom domain:** same Settings → Pages screen, put
   `blog.cmyksoda.cc` in the "Custom domain" box and save.

4. **Add the DNS record** at whoever manages `cmyksoda.cc`:

   | Type | Name | Value | Proxy |
   | --- | --- | --- | --- |
   | `CNAME` | `blog` | `cmyksoda.github.io` | **DNS only** (grey cloud) |

   DNS can take anywhere from a few minutes to a few hours to spread.

   **The proxy setting matters.** GitHub gets your HTTPS certificate by
   having Let's Encrypt send a request to `blog.cmyksoda.cc` and confirm
   it reaches GitHub. With Cloudflare's orange cloud on, Cloudflare
   answers that request itself, the check never reaches GitHub, and the
   certificate never issues — "Enforce HTTPS" stays greyed out forever.

   If you ever *do* want to turn the proxy on later:
   - Wait until the certificate has issued, then switch it on.
   - Set **SSL/TLS → Full** (or Full strict) in Cloudflare **first**. On
     **Flexible**, Cloudflare talks HTTP to GitHub, GitHub redirects to
     HTTPS, and you get an infinite redirect loop that takes the whole
     site down. This is the classic GitHub Pages + Cloudflare trap.
   - Expect to purge Cloudflare's cache after pushing, or your new posts
     may not appear for a while. This is the main reason it's usually not
     worth proxying a blog.

5. Once GitHub says the domain is verified, tick **Enforce HTTPS**. The
   certificate is free and automatic, but it can take up to 24 hours to
   issue — if it's greyed out, it's not broken, just wait.

### After that

Publishing is just:

```bash
git add -A
git commit -m "add a post about the thing"
git push
```

GitHub rebuilds and deploys in about a minute. Watch it happen in the
**Actions** tab of the repo — a green tick means it's live, a red X means
something broke and clicking it shows why.

---

## Keeping things up to date

**Hugo** (you have 0.164.0 in `~/.local/bin/hugo`):

```bash
curl -sL https://github.com/gohugoio/hugo/releases/latest/download/hugo_extended_linux-amd64.tar.gz \
  | tar -xz -C ~/.local/bin hugo
```

If you update Hugo, change `HUGO_VERSION` in
`.github/workflows/deploy.yml` to match, so the live site builds with the
same version you tested with.

**The theme** — you're on risotto v0.5.1. To update:

```bash
rm -rf themes/risotto
git clone --depth 1 --branch v0.6.0 https://github.com/joeroe/risotto themes/risotto
rm -rf themes/risotto/.git themes/risotto/exampleSite themes/risotto/images
hugo server   # check nothing broke
```

Because all your changes live outside `themes/`, this is safe. The one
thing to check afterwards is `layouts/partials/head.html`, since that's a
modified copy of the theme's — compare it against the new version.

---

## When something goes wrong

| Symptom | Almost always |
| --- | --- |
| Post missing from the live site | still `draft = true` |
| Post missing locally too | run `hugo server -D` to include drafts |
| Site works locally, unstyled online | `baseURL` in `hugo.toml` is wrong — it must match your real address exactly, **including `https://`** |
| Site suddenly unstyled right after a settings change | mixed content — the page loads over `https://` but links `http://` assets, so the browser blocks the CSS. Re-run the deploy |
| Image not showing | filename typo, or wrong folder — case matters! |
| Tag page 404s | tag spelled differently in another post |
| A section heading is lowercase (`posts` not `Posts`) | that section has no `_index.md`, so Hugo used the folder name. Add `content/<section>/_index.md` with a `title` |
| Changes not appearing | hard-refresh (`Ctrl+Shift+R`) |
| Deploy failed | Actions tab → click the red X → read the log |

`hugo server` prints errors in the terminal with a line number. They're
usually clearer than they look — most often a typo in front matter, or a
missing `+++`.

---

## Getting the repo up for the first time

```bash
cd ~/Documents/Blog
git add -A
git commit -m "Set up Hugo blog with risotto theme"
gh repo create blog --public --source=. --remote=origin --push
```

Then do the Pages + DNS steps above.
