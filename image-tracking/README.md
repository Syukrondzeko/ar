# Image Tracking

Point the camera at a **normal picture** and the thing in it appears as a real **3D model**, with no
black-and-white marker needed. This is how AR posters, AR book covers and AR textbooks work.

| Picture | 3D model that appears |
|---|---|
| `targets/eiffel.jpg` | 🗼 Eiffel Tower (with its park) |
| `targets/pisa.jpg` | 🗼 Leaning Tower of Pisa |

Both pictures work at the same time: point the camera at either one.

You can also **use your own picture** on the start screen. The page turns it into a tracking target right
in the browser (10–60 seconds), and a dancing robot appears on it.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app: start screen, in-browser picture compiler, AR view |
| `targets/eiffel.jpg`, `targets/pisa.jpg` | The pictures to point the camera at |
| `targets/targets.mind` | Both pictures compiled into one tracking file (order: eiffel = 0, pisa = 1) |
| `models/eiffel.glb`, `models/pisa.glb` | The 3D models, compressed for phones |
| `models/source/` | Original downloads from Sketchfab (kept locally, not uploaded to GitHub) |

The robot for your own picture is shared with Hiro Model: `../hiro-model/models/robot.glb`.

## Built with

- [A-Frame](https://aframe.io) 1.5.0: build 3D scenes with HTML tags
- [MindAR](https://hiukim.github.io/mind-ar-js-doc/) 1.2.5: image tracking, plus the Compiler that turns a picture into a `.mind` target
- [aframe-extras](https://github.com/c-frame/aframe-extras) 7.2.0: `animation-mixer` for the robot's animations

## How to run

```bash
cd ar   # your project folder
python3 -m http.server 8000
```

Open `http://localhost:8000/image-tracking/`, tap **▶ Start**, then show one of the pictures to the camera
(open it on another screen, or print it). On a phone, use the HTTPS site (GitHub Pages) instead.

## How it works

### 1. A picture becomes a `.mind` file
MindAR can't track a picture directly. It first **compiles** it: it finds hundreds of feature points
(corners, edges, textures) and saves them in a `.mind` file. That's why detailed pictures track well and
plain ones don't.

```js
const compiler = new window.MINDAR.IMAGE.Compiler();
await compiler.compileImageTargets([img1, img2], (percent) => console.log(percent));
const mindData = compiler.exportData(); // the .mind file contents
```

Several pictures can go in one `.mind` file. Each gets a `targetIndex` (0, 1…) in the order you
compiled them.

### 2. Each picture shows its own 3D model
```html
<a-scene mindar-image="imageTargetSrc: targets/targets.mind; maxTrack: 1">
  <a-entity mindar-image-target="targetIndex: 0">                      <!-- eiffel.jpg -->
    <a-entity gltf-model="url(models/eiffel.glb)" fit-model="size: 0.9; by: height"></a-entity>
  </a-entity>
</a-scene>
```

- The picture is **1 unit wide**, and its height is `height / width` (the "aspect").
- `x` = right, `y` = up the picture, `z` = out of the picture toward you.
- `fit-model` (custom component in `index.html`) resizes any model so it's 0.9 units tall, whatever
  size the artist made it. The Eiffel model was about 300,000 units tall!
- When a picture is found, the model **pops in** (grows from nothing), then **spins slowly** so you
  can see it's really 3D. **⏸️ Stop spin** stops it.

### 3. Screen mode vs table mode
- 🖥️ **Screen mode** (default): for an upright picture (laptop, phone, wall). The model stands on the bottom edge, in front of the picture.
- 🛋️ **Table mode**: for a picture lying flat. The model is rotated 90° so it stands *out of* the picture.

## Add another picture + model

1. **Picture:** put it in `targets/` (JPG, about 1000 px max).
2. **Model:** download a `.glb` (e.g. from [Sketchfab](https://sketchfab.com): filter Downloadable + Creative Commons,
   pick glTF Binary, under ~500k triangles). Put the original in `models/source/<name>.glb`.
3. **Compress it** for phones (WebP textures, max 1024 px):
   ```bash
   npx @gltf-transform/cli optimize models/source/<name>.glb models/<name>.glb \
     --compress false --instance false --palette false --simplify false \
     --texture-compress webp --texture-size 1024
   ```
4. **Recompile `targets.mind`** with *all* pictures, e.g. with MindAR's online compiler:
   https://hiukim.github.io/mind-ar-js-doc/tools/compile (upload them in order, download `targets.mind`).
5. **Add it to `READY_TARGETS`** in `index.html`, in the same order as compiled:
   ```js
   { label: "Big Ben", image: "targets/bigben.jpg", aspect: height / width, model: "bigben", scale: 1.0 },
   ```
6. **Add the credit** below if the license is CC BY.

## Tips for good tracking

- ✅ Lots of detail, sharp edges, strong contrast: posters, book covers, textured photos.
- ❌ Plain colors, simple logos, repeating patterns, very dark images.
- Keep the picture flat and well lit, show the whole picture, and avoid glare on screens.

## Troubleshooting

- **Stuck on "Point the camera at a picture":** move closer so the picture fills about half the screen, add light, reduce glare.
- **Model shakes:** hold the phone steadier, or use a more detailed picture.
- **Picture found but no model:** check the browser console for a 404 on `models/<name>.glb`.
- **"Could not prepare this picture":** try a different image, or a smaller one.
- **Camera error:** use `localhost` on the laptop or the HTTPS site on a phone, and allow the camera.

## Credits

3D models from Sketchfab, licensed under [CC BY 4.0](http://creativecommons.org/licenses/by/4.0/).
They were compressed for the web (WebP textures, max 1024 px) but otherwise unchanged.

- **"Eiffel Tower - Toshueyi"** by [Joanthan To](https://sketchfab.com/Toshueyi),
  [source](https://sketchfab.com/3d-models/eiffel-tower-toshueyi-3a1c5d841d424ee8a8c18ae077473e41)
- **"Leaning tower of Pisa"** by [Aglaiapoulida](https://sketchfab.com/Aglaiapoulida),
  [source](https://sketchfab.com/3d-models/leaning-tower-of-pisa-f0456bc36bdc4bfd8890f0dfdaf86656)
