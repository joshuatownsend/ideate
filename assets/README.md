# assets

| File | Purpose |
| --- | --- |
| `social-card.html` | Source for the social card. Self-contained: no external fonts, images, or scripts. |
| `social-card.png` | Rendered card, 1280×640 — the size GitHub wants for a repo social preview. |

## Regenerating the PNG

The card is a fixed 1280×640 page, so a plain viewport screenshot at that size is
the whole render step:

```sh
# from the repo root
python -m http.server 8791 --directory assets &
npx playwright screenshot \
  --viewport-size=1280,640 \
  http://127.0.0.1:8791/social-card.html \
  assets/social-card.png
```

Any headless-Chrome screenshot tool works; the only requirements are a 1280×640
viewport and no full-page flag.

## Where it's used

- Shown at the top of the repo `README.md`.
- Uploaded as the repo's Open Graph image under **Settings → General → Social
  preview**. That upload is manual and does *not* update when this file changes —
  re-upload after regenerating.
