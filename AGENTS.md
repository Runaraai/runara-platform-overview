# AGENTS.md — runara-platform-overview (Runara Engineering Docs)

Guidance for any coding assistant (Claude Code, Codex, Cursor, etc.) adding or
editing pages in this repo.

## What this repo is

A static site published via GitHub Pages at
**https://runaraai.github.io/runara-platform-overview/**. Each page is a
single self-contained `.html` file (own `<style>`, no shared CSS file, no
build step) — most started life as a Claude Artifact and were republished
here as plain static pages. `index.html` is the landing page and must link
every other page.

There is no build/CI step. Pushing to `master` deploys automatically —
GitHub Pages rebuilds and serves the new content within roughly a minute.
**Treat every commit to `master` as a production deploy.**

## Adding a new page

1. Write the page as one self-contained `<file-name>.html` — inline
   `<style>`, no external JS/CSS except Google Fonts and (if you need
   diagrams) Mermaid from a CDN (see below). Don't add a shared stylesheet;
   each page keeps its own copy of its theme variables, by design, so pages
   stay independently copy-pasteable.
2. Add the nav snippet (below) so Home/Top work like every other page.
3. Add a `<li><a href="your-file.html">...</a></li>` entry to `index.html`'s
   `ul.docs` list — this is the only index of pages; a page not linked from
   here is effectively invisible.
4. Commit and push to `master`. That's the entire deploy.

## The nav snippet every content page needs

`index.html` is the home page and does **not** get this (there's nowhere
"home" to link to). Every other page should have both a Home link
(top-right) and a back-to-top link (bottom-right), fixed to the viewport.

Add to the page's `<style>` block — reuse whatever the page already calls
its accent-color variable (`--accent` in most pages, `--new` in the
scale-out roadmap page; check the page's `:root{...}` block first):

```css
.site-home{position:fixed; top:14px; right:16px; z-index:1000; font-family:var(--mono); font-size:12px; color:var(--accent); text-decoration:none; border:1px solid var(--border); border-radius:6px; padding:7px 12px; background:var(--surface)}
.site-home:hover{border-color:var(--accent)}
.to-top{position:fixed; bottom:18px; right:16px; z-index:1000; font-family:var(--mono); font-size:12px; color:var(--fg-dim); text-decoration:none; border:1px solid var(--border); border-radius:6px; padding:7px 12px; background:var(--surface)}
.to-top:hover{color:var(--accent); border-color:var(--accent)}
html{scroll-behavior:smooth}
@media (max-width:560px){ .site-home,.to-top{font-size:11px; padding:6px 10px} }
```

And right after `<body>` (or right after the page's own `<style>` block,
whichever comes first in that file's layout), before the page's main
wrapper div:

```html
<a class="site-home" href="index.html">&larr; Home</a>
<a class="to-top" href="#top">&uarr; Top</a>

<div class="wrap" id="top">
```

The `id="top"` goes on whatever element is currently the outermost content
wrapper (usually `.wrap` or `.container`) — add it there rather than
creating a new element, and make sure there's only one `id="top"` on the
page.

## Mermaid diagrams

Pages republished from Claude Artifacts sometimes include a
`<pre class="mermaid">...</pre>` block plus a runtime script tag pointing at
`/_runtime/mermaid-<version>.min.js` — that path only exists on claude.ai
and will 404 here. Repoint it at the matching version on jsdelivr instead:

```html
<script src="https://cdn.jsdelivr.net/npm/mermaid@<version>/dist/mermaid.min.js"></script>
```

Keep the version number identical to what the artifact shipped with
(check the original `/_runtime/mermaid-X.Y.Z.min.js` filename) — don't
silently bump it.

## Conventions worth keeping

- One file per page, no shared layout/include system. Looks redundant, but
  it means any single page can be read, edited or copy-pasted in isolation
  without hunting for a shared template.
- Page titles: a short name (2–4 words), set via `<title>`, matching what
  `index.html`'s list entry calls it.
- Don't invent a new CSS variable naming scheme per page for the nav
  snippet — reuse the page's existing `--accent`/`--new`/`--fg-dim`/etc. so
  the floating Home/Top pills match that page's palette instead of clashing
  with it.
- This is a public repo under the `Runaraai` org. Don't push anything here
  that shouldn't be public (internal credentials, unredacted customer data,
  etc.) — these pages are meant to be publicly readable engineering docs,
  not a dumping ground for anything drafted internally.
