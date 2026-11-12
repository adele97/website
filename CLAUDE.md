# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal website for a conference speaker: plain static HTML and CSS, hosted on GitHub Pages with a custom domain and deployed from a branch. There is no build step, no package manager, and no tests or linter. The files in the repo are exactly what gets served.

## Hard constraints (from the owner)

- **No JavaScript.** That means none on the site and none in the tooling: no npm, Node, or static site generators. An Eleventy setup was tried and rejected. If something seems to need JS, do it in CSS (see the slideshow below). The only JS on the site comes from embedded YouTube iframes.
- **Do not commit.** Leave git operations to the owner.
- **Stay inside this directory.** Put temporary files in the session scratchpad.
- **Accessibility is a requirement.** Keep the skip link, landmarks, `aria-current="page"` in the nav, alt text, visible `:focus-visible` outlines, `prefers-reduced-motion` handling, and WCAG AA contrast.
- **Layout is full-width and responsive, with no "boxes" (cards or panels) around content.** Use full-bleed colour bands instead. Paragraphs are capped at `70ch` for readability.

## Preview

Open `index.html` directly in a browser. YouTube embeds fail with "Error 153" over `file://`, so to test them serve over http:

```sh
python3 -m http.server 3333   # owner prefers port 3333
```

Headless Chrome won't render narrower than 500px. To check phone widths, screenshot a wrapper page that loads the site in a 390px-wide `<iframe>`.

## Structure and conventions that span files

- **The header and footer are duplicated in every page.** This is deliberate, because there's no include mechanism. Any change to `<header class="site-header">` or `<footer class="site-footer">` must be made in every HTML file. Each page marks its own nav link with `aria-current="page"`.
- **All links are relative and depend on the page's folder.** The relative links are what make GitHub Pages and `file://` both work.
  - `index.html` (root) uses `assets/...` and `pages/videos.html`.
  - Files in `pages/` use `../assets/...` and `../index.html`.
  - New pages go in `pages/`.
- **The colour system lives in `assets/css/styles.css`.**
  - Palette tokens: slate `#5F7470`, salmon `#FF928B`, mint `#8BE8CB`, periwinkle `#89A1EF`, ink `#1E1E24`, plus `--mint-tint` as the light page background.
  - Sections use `.band .band-{mint|salmon|periwinkle|slate}`. Each band class redefines `--text`, `--link`, `--heading`, `--focus` and the `--button-*` variables so contrast stays valid inside it. The bands look the same in light and dark mode; only the page background and text flip in dark mode.
  - Mint, salmon and periwinkle fail contrast as text on light backgrounds. Use them only as fills behind ink text, or as decoration.
  - Check the contrast ratio of any new colour pairing before using it. The comment at the top of the palette section lists the verified ratios.
  - The owner wants the palette used visibly, not mostly white and black.
- **The hero slideshow is CSS only** (in `index.html` and `styles.css`).
  - Slides are `<li><figure><img><figcaption class="photo-credit">` items. Each photo carries its own credit, so the credit fades with its photo.
  - Slide 1 is a static base layer; the later slides fade in through the `hero-fade` keyframes with `nth-child` delays.
  - Adding or removing slides means updating `--cycle`, the keyframe percentages and the delays.
  - The "Pause slideshow" checkbox (needed for WCAG 2.2.2) pauses the animation through `:has(#pause-slides:checked)`. It's commented out in the HTML while there's only one photo; uncomment it when adding a second.
  - With reduced motion, only the first slide shows.
  - Below 48rem the photo sits above a slate text band; from 48rem up the text overlays the photo on an ink gradient.
- **Videos** (`pages/videos.html`) are `<li class="video-card">` blocks with a `youtube-nocookie.com` iframe.
  - Each video's ID appears three times: the embed `src`, the "Watch on YouTube" link and the `thumbnailUrl` `<link>`.
  - Its title appears three times: the `<h2>`, the iframe `title` attribute, and a `.visually-hidden` span inside the "Watch on YouTube" link. The span keeps the identical link texts distinguishable for screen readers.
- **Hero text contrast over the photo** depends on two ink layers in `.hero-content` (from 48rem up):
  - a light bottom-up fade on the element itself;
  - a darker patch on `::before`, behind the text only. It fades out to the right after `gutter + 48rem` and towards the top via `mask-image`.
  - Together they keep the mint `h1` at ≥5.3:1 and body text and buttons at ≥9:1, even over a white pixel.
  - The owner found full-width shading too heavy. Keep the photo clear outside the text area.
  - Re-measure the text extents if the hero layout or copy changes.
- **Footer social links** (LinkedIn, Bluesky) use inline SVG icons from Simple Icons (CC0) with `fill="currentColor"` and `aria-hidden="true"`, next to visible text labels.
  - Keep the icons inline. CSS `mask-image` with SVG files doesn't load over `file://`, and inline SVG also stays visible in forced-colours mode.
  - The footer is slate, so its links must be white. Ink on slate fails contrast.
- **SEO and agents.** Each page's `<head>` has a `<title>`, a meta description, a `rel="canonical"` link and Open Graph/Twitter tags. These use absolute `https://adelecarpenter.com/...` URLs, which is the one exception to the relative-links rule. New pages need all of these, plus an entry in `sitemap.xml` and `llms.txt`.
  - **Favicon:** every `<head>` links `assets/favicon.svg`, `assets/favicon-48.png` and `assets/apple-touch-icon.png` (relative paths). `/favicon.ico` at the root is the fallback for crawlers. The icon is "AC" in ink on mint, with the letters as Lato Black outlines so no font is needed. If you change the SVG, regenerate the PNGs and the ICO from it.
  - Structured data is schema.org **microdata** (`itemscope`/`itemprop`), not JSON-LD, because JSON-LD needs a `<script>` tag. On the home page the bio `<section>` is the `Person`; its hidden `<meta>`/`<link>` tags come after the `<h2>` so that `.band h2:first-child` still matches. Each `.video-card` is a `VideoObject`.
  - `robots.txt` lets search engines and AI search/assistant agents in and blocks AI training crawlers. That is the owner's chosen balance ("findable, not training data").
  - `llms.txt` is a plain Markdown summary for agents. Keep it factual and limited to what the site already says publicly.
- **Fonts:** Lato is self-hosted from `assets/fonts/Lato/` through `@font-face` (the OFL licence file is kept there).

## Images

- The full-size originals (30–40 MB, at the repo root) are gitignored and must never be modified.
- Web copies go in `assets/images/`. To make one:
  - Resize with macOS `sips` to 1920px wide at JPEG quality around 60, aiming for under about 400 KB: `sips -s format jpeg -s formatOptions 60 --resampleWidth 1920 <orig> --out assets/images/<name>.jpg`.
  - Pillow and ImageMagick aren't installed. For edits like blurring a region, use a Swift script with CoreImage.
- Hero photos are cropped by `object-fit: cover`. Tune the crop per image with an inline `object-position`.
- Photo credits follow the pattern "Photo: Joni Israeli / Tweakers Dev Summit".
- `assets/images/README.md` lists the expected filenames.
