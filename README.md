# davidcwei.com

A static site. No build step, no framework, no JavaScript — plain HTML plus one
stylesheet, served by GitHub Pages from this repo.

## Structure

```
index.html          about (the landing page)
quotes.html
projects.html
podcast.html
writing/
  index.html        essay list, grouped Pinned / recent / Older
  <slug>.html       one file per essay
  1.html … 11.html  redirect stubs from the old numbered URLs
styles.css          the site's only stylesheet
images/             all images, self-hosted
rss.xml             feed for the essays
CNAME               custom domain
```

## Publishing

Edit a file, then:

```
git add -A && git commit -m "message" && git push
```

GitHub Pages redeploys within a minute or so.

## Responsive foundations

Mobile support is structural, not something to reapply per page. Four rules
carry it, and new content inherits them automatically:

1. **One stylesheet.** Pages contain no `<style>` blocks and no `style="..."`
   attributes. Everything lives in `styles.css`, so a fix lands once and
   applies everywhere. Keep it that way.
2. **Fluid column.** `.page` is capped with `max-width` in `rem` and padded
   with `clamp()`. Below the cap it simply fills the screen — no width media
   query needed. Body type is `clamp()`-scaled, so it shrinks on phones and
   settles at 18px on desktop.
3. **Defensive base rules.** Images, video and iframes are capped at
   `max-width: 100%`; long unbroken strings wrap rather than forcing a
   horizontal scrollbar; `pre` and `table` scroll inside themselves. A new
   image, embed or wide table cannot break the layout.
4. **`--micro` token.** Every small mono label (dates, group headings,
   captions, footer links) uses `var(--micro)`, which is raised for narrow
   screens in a single place. New labels should use the token, never a
   literal size.

Only two intentional layout breakpoints exist, both at `30rem`: the `--micro`
bump, and the essay list stacking its date above the title.

## Checking a change

Load a page at phone width and paste this into the browser console. It reports
anything wider than the viewport and any tap target under 30px tall — both
should be zero:

```js
(() => {
  const vw = document.documentElement.clientWidth, over = [], small = [];
  document.querySelectorAll('body *').forEach(el => {
    const r = el.getBoundingClientRect();
    if (r.width > vw + 1 || r.right > vw + 1 || r.left < -1) over.push(el.tagName + '.' + el.className);
  });
  document.querySelectorAll('a').forEach(a => {
    const r = a.getBoundingClientRect();
    if (r.height > 0 && r.height < 30) small.push(a.textContent.trim());
  });
  return { viewport: vw, scrollWidth: document.documentElement.scrollWidth, overflowing: over, smallTargets: small };
})()
```

## Security

- Every content page carries a Content-Security-Policy meta locked to
  `'self'` — no third-party scripts, styles, fonts or images can load, and
  the site itself contains no JavaScript. Keep new pages on this policy.
- Fonts are self-hosted in `fonts/` (also why the site works in mainland
  China). The site makes zero external requests.
- HTTPS is enforced at the GitHub Pages level.

## Performance

- Images are resized to at most 1400px wide (2x the widest the column
  renders) and recompressed. Every `<img>` carries intrinsic
  `width`/`height` (no layout shift), `loading="lazy"` below the fold,
  and `decoding="async"`. Run any new image through the same treatment.
- The two latin font files are preloaded; latin-ext subsets load only if
  a page ever uses those characters.

## Notes

- Essay content was imported from an older Squarespace site; original
  publication dates are preserved.
