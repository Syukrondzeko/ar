# My AR Projects

Small WebAR and WebVR projects for learning augmented and virtual reality in the browser. No app install needed.

**Live site:** https://syukrondzeko.github.io/ar/

| Folder | Project | Type |
|---|---|---|
| [`hiro-box/`](hiro-box/README.md) | Spinning box on the Hiro marker | Marker-based AR (AR.js) |
| [`hiro-model/`](hiro-model/README.md) | Animated robot and fox (.glb) on the Hiro marker | Marker-based AR + 3D models |
| [`image-tracking/`](image-tracking/README.md) | Pictures of the Eiffel Tower and Tower of Pisa come to life as real 3D models (or upload any picture) | Image tracking (MindAR) + 3D models |
| [`hand-control/`](hand-control/README.md) | Grab, zoom, rotate and poke 3D models with your hands; the robot reacts to hand signs | Hand tracking AI (MediaPipe) + 3D models |
| [`vr-museum/`](vr-museum/README.md) | Walk around a virtual landmark museum with a robot guide | VR (WebXR-ready) |
| [`face-filter/`](face-filter/README.md) | 8 face filters: cool, cat, royal, devil, angel, party, vintage, noir | Face tracking (MindAR) + camera color effects |

## Run everything

```bash
cd ar   # your project folder
python3 -m http.server 8000
```

Then open `http://localhost:8000/` for a menu that links to each project.

The camera only works on `localhost` or HTTPS. To use a phone as the AR device,
use `ngrok http 8000` or host the folder on GitHub Pages.
