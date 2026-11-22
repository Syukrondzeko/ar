# Hiro Model

A real animated 3D character standing on the **Hiro marker**. Choose a model at the top and an
animation at the bottom.

| Model | Animations |
|---|---|
| 🤖 Robot | Dance, Wave, Jump, Walking, Running, ThumbsUp, Punch, Death and more |
| 🦊 Fox | Survey, Walk, Run |

## Files

| File | What it is |
|---|---|
| `index.html` | The AR page |
| `models/robot.glb` | Animated robot character |
| `models/fox.glb` | Animated fox |

The marker is shared with Hiro Box: the 🎯 Marker button opens `../hiro-box/marker.html`.

## Built with

- [A-Frame](https://aframe.io) 1.5.0: build 3D scenes with HTML tags
- [AR.js](https://ar-js-org.github.io/AR.js-Docs/) 3.4.5: marker tracking
- [aframe-extras](https://github.com/c-frame/aframe-extras) 7.2.0: `animation-mixer`, which plays animations stored inside a `.glb`

## How to run

1. Start a local server from the `ar` folder:
   ```bash
   cd ar   # your project folder
   python3 -m http.server 8000
   ```
2. On the Mac, open `http://localhost:8000/hiro-model/` and allow the camera.
3. On your phone (same Wi-Fi), open `http://<your-mac-ip>:8000/hiro-box/marker.html`.
4. Hold the marker in front of the webcam.

The `.glb` files must be loaded through the server. Opening `index.html` directly as a file won't work.

## How it works

```html
<a-assets>
  <a-asset-item id="robot" src="models/robot.glb"></a-asset-item>   <!-- preload the file -->
</a-assets>

<a-marker preset="hiro">
  <a-entity
    gltf-model="#robot"                         <!-- show the model -->
    fit-model="size: 1.2"                       <!-- auto-size it (custom component) -->
    animation-mixer="clip: Dance; loop: repeat" <!-- play an animation from inside the .glb -->
  ></a-entity>
</a-marker>
```

- **`.glb`** is the standard 3D file format for the web. One file holds the mesh, textures and animations.
- **`fit-model`** is a small custom component in `index.html`. Every model is made at a different scale
  (the fox is about 100× bigger than the robot), so it measures the model and scales it to `size`
  marker-widths, then puts its feet on the marker.
- **Animation buttons** are built automatically: when a model loads, the page reads
  `getObject3D("mesh").animations` and makes one button per clip.

## Add your own model

1. Download an animated `.glb`, for example from [Sketchfab](https://sketchfab.com) (filter: Downloadable + Animated),
   [Poly Pizza](https://poly.pizza) or [Mixamo](https://www.mixamo.com) (characters with animations).
2. Put it in `models/`, e.g. `models/dragon.glb`.
3. Add it to `<a-assets>`: `<a-asset-item id="dragon" src="models/dragon.glb"></a-asset-item>`
4. Add a button: `<button data-model="dragon">🐉 Dragon</button>`.
   `data-model` must match the file name (`dragon` → `models/dragon.glb`), because the page loads `models/<name>.glb`.
5. Add its first animation to `startClip` in the script, e.g. `dragon: "Fly"`.
   (Not sure of the name? Load it once, and the animation buttons will show all clip names.)

Keep files small (under ~5 MB) so they load quickly on phones.

## Model credits

- **Robot** ("RobotExpressive"): by [Tomás Laulhé](https://www.patreon.com/quaternius), CC0 (public domain),
  from the three.js examples.
- **Fox**: model by PixelMannen (CC0), rigging and animation by tomkranis
  ([CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)), glTF conversion by @AsoboStudio and @scurest,
  from the [Khronos glTF Sample Assets](https://github.com/KhronosGroup/glTF-Sample-Assets/tree/main/Models/Fox).

## Troubleshooting

- **Marker found, but no model:** check the browser console for a 404. The page must be served from the `ar` folder.
- **Model too big or small:** change `fit-model="size: 1.2"`.
- **Model doesn't move:** that model has no animations, or the clip name in `startClip` doesn't match.
