# Face Filters

Instagram-style face filters that follow your face through the webcam. Pick a filter from the
bar at the bottom of the screen. No marker needed.

## Filters

| Filter | What you get |
|---|---|
| 🚫 None | Plain camera |
| 😎 Cool | Sunglasses, top hat, pulsing clown nose |
| 🐱 Cat | Cat ears, pink nose, whiskers |
| 👑 Royal | Gold crown with jewels |
| 😈 Devil | Red horns, pointy goatee, red-tinted camera |
| 😇 Angel | Floating glowing halo, soft bright camera |
| ✨ Party | Colorful shapes orbiting your head, punchy colors |
| 🥸 Vintage | Mustache, gold monocle, old-photo (sepia) camera |
| 🎬 Noir | No objects, black-and-white movie look |

The filters mix two kinds of effects:
- **3D objects** attached to points on your face (A-Frame shapes)
- **Camera color effects**: a CSS `filter` on the camera video (sepia, grayscale, hue-rotate…)

## Files

| File | What it is |
|---|---|
| `index.html` | The face filter page with all filters and the picker |

## Built with

- [A-Frame](https://aframe.io) 1.5.0: build 3D scenes with HTML tags
- [MindAR](https://hiukim.github.io/mind-ar-js-doc/) 1.2.5: face tracking (uses Google's MediaPipe face mesh, 468 points)

## How to run

1. Start a local server from the `ar` folder:
   ```bash
   cd ar   # your project folder
   python3 -m http.server 8000
   ```
2. Open `http://localhost:8000/face-filter/` in Safari or Chrome and allow the camera.
3. Look at the webcam, then tap a filter at the bottom.

The camera only works on `localhost` or HTTPS, not on the IP address. To run it on a phone, use
`ngrok http 8000` or host it on GitHub Pages.

## How it works

Each filter is one wrapper entity with `class="filter"`. Inside, each `mindar-face-target`
sticks to one point on the face:

```html
<a-entity class="filter" id="cat">                    <!-- one filter -->
  <a-entity mindar-face-target="anchorIndex: 1">      <!-- tip of nose -->
    <a-sphere radius="0.05" color="#ff6f91"></a-sphere>
  </a-entity>
</a-entity>
```

| anchorIndex | Point on the face |
|---|---|
| `168` | Between the eyes |
| `10` | Top of forehead |
| `1` | Tip of nose |
| `0` | Upper lip |
| `152` | Chin |

- The face is about **1 unit wide**, so sizes are small numbers like `0.1`.
- `position="x y z"`: `x` is left/right, `y` is up/down, `z` is toward (+) or away from (−) the face.
- Picking a filter shows its wrapper and hides the others (`setAttribute("visible", ...)`).
  MindAR itself shows and hides each `mindar-face-target` on every frame, so the toggle has to be
  on the wrapper, not on the target.
- `data-video="sepia(0.9) ..."` on a wrapper sets a CSS filter on the camera video.
- MindAR hides objects that are behind your head (the face "occluder"). That's why the Party shapes
  disappear when they orbit behind you.

## Add your own filter

1. Copy one `<a-entity class="filter" id="...">` block and give it a new `id`.
2. Add a button in `#picker`: `<button data-filter="your-id"><span>🦄</span>Name</button>`.
3. Optionally add `data-video="..."` for a color effect. Try `hue-rotate(90deg)` or `invert(1)`.

## Troubleshooting

The status label at the top shows what the tracker is doing.

- **Slow to start:** the tracking library and face model are several MB and download on first load.
- **Laggy or choppy:** face tracking runs a neural network on every frame. Chrome is usually smoother than Safari.
- **Camera blocked / error:** use `localhost`, allow the camera, and close other apps using the webcam.
- **Items in the wrong place:** adjust `position`, `radius` and `height` in `index.html`.
