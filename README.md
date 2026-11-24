# Lorem Ipsum Julsida

A hand-written 90s-style Christmas site. It's plain HTML and CSS, with no build step and no dependencies. The only JavaScript is the Doom game, which runs in its own page inside an iframe.

## Run locally

```sh
python3 -m http.server
```

Then open http://localhost:8000/. Always use the server: opened as `file://`, the game won't load and YouTube shows "Error 153", because browsers send no referrer from local files.

## Pages

| File | What it is |
|---|---|
| `index.html` | Landing page / blog (lorem ipsum posts) |
| `doom.html` | The game page. It embeds `doom/play.html`; see `doom/README.md` to get the game running |
| `videos.html` | Julvideor: the dog choir and cat choir YouTube embeds |

## Shared layout

There are no includes. The header, left nav, right column and footer are copied into every page and marked with `SHARED ... (copy to every page)` comments. When you change one, change all three.

## Images

| Folder | Contents |
|---|---|
| `img/clipart/` | Public-domain and CC0 Christmas clipart from Wikimedia Commons, credited in `CREDITS.md` |
| `img/placeholder/` | Labeled boxes marking spots for real images. Swap the `src` when you have one |
| `img/buttons/`, `img/badges/`, `img/*.svg` | Nav buttons, 88×31 badges, background, counter, dividers and the blinking construction sign, all drawn for this site |

When you add new clipart, add a line to `CREDITS.md` with its source and license.
