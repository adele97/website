# Adele Carpenter – speaker website

A static website built with plain HTML and CSS. There's no JavaScript and no build step: the files in this folder are the site.

## Preview

Open `index.html` in a browser.

The embedded YouTube players only play when the page is served over http(s). To test them locally, run:

```sh
python3 -m http.server 3333
```

Then open http://localhost:3333.

## Publish (GitHub Pages)

Settings → Pages → "Deploy from a branch". Then set the custom domain in the same screen.

## Where things live

| To change…            | Edit                                   |
|-----------------------|----------------------------------------|
| Home page             | `index.html`                           |
| Videos                | `pages/videos.html` (copy a `<li class="video-card">` block per video) |
| Styles and colours    | `assets/css/styles.css`                |
| Images                | `assets/images/` (see the README there) |

## Header and footer are copied into every page

Each page has its own copy of the `<header class="site-header">` and `<footer class="site-footer">` blocks. When you change them, update **every page**.

Links are relative, so they differ per folder:

- `index.html` links to `pages/videos.html` and loads `assets/...`
- files in `pages/` link to `../index.html` and load `../assets/...`

Mark the current page in the menu with `aria-current="page"`.
