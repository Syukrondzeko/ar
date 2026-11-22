# Hiro Box

A spinning blue box with "Hello AR!" text that appears on top of the **Hiro marker**.
This is marker-based AR: the camera looks for a known black-and-white pattern and places 3D content on it.

## Files

| File | What it is |
|---|---|
| `index.html` | The AR page (uses the camera). The 🎯 Marker button at the top right opens the marker page |
| `marker.html` | Shows the Hiro marker, open it on a second device |
| `hiro.png` | The Hiro marker image (you can also print it) |

## Built with

- [A-Frame](https://aframe.io) 1.5.0: build 3D scenes with HTML tags
- [AR.js](https://ar-js-org.github.io/AR.js-Docs/) 3.4.5: marker tracking for A-Frame

## How to run

1. Start a local server from the `ar` folder:
   ```bash
   cd ar   # your project folder
   python3 -m http.server 8000
   ```
2. On the Mac, open `http://localhost:8000/hiro-box/` in Safari or Chrome and allow the camera.
3. On your phone (same Wi-Fi), open `http://<your-mac-ip>:8000/hiro-box/marker.html`.
   Find your Mac's IP with `ipconfig getifaddr en0`.
4. Hold the phone screen 20–40 cm in front of the webcam.

The camera only works on `localhost` or HTTPS. To run the AR page on a phone, use
`ngrok http 8000` or host it on GitHub Pages.

## How it works

```html
<a-marker preset="hiro">   <!-- everything inside follows the marker -->
  <a-box ...></a-box>
  <a-text ...></a-text>
</a-marker>
```

- `position="x y z"`: `y` is up, away from the marker surface. 1 unit is about the marker's width.
- `animation="property: rotation; to: 0 360 0; ..."` makes the box spin. `dur` is in milliseconds.

## Ideas to try

- Change `color="#4CC3D9"` to another color.
- Swap `<a-box>` for `<a-sphere radius="0.5">`, `<a-cylinder>` or `<a-torus>`.
- Change `dur: 3000` to spin faster or slower.
- Replace the box with a 3D model: `<a-gltf-model src="model.glb"></a-gltf-model>`.

## Troubleshooting

- **Nothing appears:** keep the whole black border visible, add light, and reduce glare on the phone screen.
- **Flickering:** hold the marker still, or print it on paper (paper tracks better than a screen).
