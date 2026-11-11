# Images

Drop your photos in this folder using these exact names (or update the `src` in the HTML).

| File          | Used on      | Purpose                                   | Suggested shape  |
|---------------|--------------|-------------------------------------------|------------------|
| hero-01.jpg   | index.html        | Hero slideshow, shown first (and the only one shown when the visitor has turned off animations in their system settings) | 16:9 landscape |
| hero-02.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |
| hero-03.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |
| hero-04.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |
| hero-05.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |
| hero-06.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |
| hero-07.jpg   | index.html        | Hero slideshow                            | 16:9 landscape   |

Tips:
- Hero photos are cropped to fill the banner, so keep the subject near the centre.
  The text box sits bottom-left and the pause control top-right — avoid faces there.
- Resize to ~1920px wide and keep each file under ~300–400 KB so the site loads fast.
- Write meaningful `alt` text for each hero photo in `index.html` (describe what's shown).
- Adding/removing hero photos: update `--cycle`, the keyframe percentages and the
  `nth-child` delays in `assets/css/styles.css` (instructions are in the comment there).
