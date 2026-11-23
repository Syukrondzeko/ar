# Hand Control

Control 3D models with your **bare hands** in front of the camera. No marker, no touching the screen.
An AI model finds your hands in every camera frame, and the page turns your finger movements into actions.

## What you can do

| Gesture | Action |
|---|---|
| 🤏 **Pinch** (thumb + index) on the model, then move | Grab it and drag it around |
| 🤏🤏 **Pinch with both hands**, pull apart / push together | Zoom in / out |
| 🤏🤏 **Pinch with both hands**, turn like a steering wheel | Rotate |
| ☝️ **Poke** the model with your index finger | Robot jumps, fox runs, towers wobble |

**Hand signs** (hold for about half a second):

| Sign | 🤖 Robot | 🦊 Fox |
|---|---|---|
| ✋ Open palm | Waves | Walks |
| 👍 Thumbs up | Thumbs up | |
| 👎 Thumbs down | Shakes head "No" | |
| ✌️ Peace | Dances | Runs |
| ✊ Fist | Punches | Sits and looks around |
| ☝️ Point up | Jumps | |
| 🤟 I love you | Nods "Yes" | |

The towers (Eiffel, Pisa) have no animations, so they only respond to grab, zoom, rotate and poke.
**↺ Reset** puts the model back in the middle.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app |

The models are shared with other projects: robot and fox from `../hiro-model/models/`, Eiffel and Pisa
from `../image-tracking/models/` (see that README for the CC BY credits).

## Built with

- [MediaPipe Gesture Recognizer](https://ai.google.dev/edge/mediapipe/solutions/vision/gesture_recognizer) 0.10.14 (Google):
  finds **21 points on each hand** and recognizes 7 hand signs. Runs in the browser, free, nothing is uploaded.
- [A-Frame](https://aframe.io) 1.5.0: the 3D scene
- [aframe-extras](https://github.com/c-frame/aframe-extras) 7.2.0: `animation-mixer` for robot and fox animations

## How to run

```bash
cd ar   # your project folder
python3 -m http.server 8000
```

Open `http://localhost:8000/hand-control/` and allow the camera. The first load downloads the hand AI
(about 18 MB), so give it a few seconds. On a phone, use the HTTPS site (GitHub Pages); it uses the front camera.

## How it works

### 1. The AI finds your hands
Every video frame goes to MediaPipe:
```js
const result = recognizer.recognizeForVideo(video, performance.now());
result.landmarks   // for each hand: 21 points {x, y, z}, from 0 to 1 across the image
result.gestures    // for each hand: the recognized sign, e.g. { categoryName: "Thumb_Up", score: 0.93 }
```

The 21 points (the ones used here):
```
        8   12  16  20      ← fingertips (pink dots)
        |   |   |   |
        6   |   |   |
        |   |   |   |
    4   5---9---13--17
     \  |
      \ |
        0                   ← wrist
```

### 2. Hand points → screen → 3D
- **To the screen:** the video is mirrored and cropped to fill the screen, so `toScreen()` applies the
  same mirror and crop to each point. That's why the drawn hand lines up with your real hand.
- **Into 3D:** `screenTo3D()` shoots a ray from the camera through that screen point and finds where it
  hits the flat plane the model stands on. Moving the model to that point makes it follow your fingers.

### 3. Detecting the gestures
- **Pinch:** the thumb tip (4) and index tip (8) are close together, compared with the hand's size
  (wrist 0 → middle finger base 9). It starts at < 0.30 and releases at > 0.45, so it doesn't flicker.
- **Grab:** a pinch that *starts* on the model's outline on screen. While pinching, the model follows.
- **Zoom / rotate:** with two pinches, the change in distance between them sets the size, and the change
  in angle sets the rotation.
- **Poke:** a straight index finger (tip farther from the wrist than its middle joint) touching the model.
- **Hand signs:** come straight from the AI, but must be held for 0.4 s before they trigger (and not
  while pinching), so passing movements don't set them off.

## Ideas to try

- Change which animation each hand sign plays in `MODELS` (e.g. `Victory: ["✌️", "Wave", "Bye!"]`).
- Make the pinch easier or harder: change `0.3` / `0.45` in the pinch detection.
- Add your own model: put a `.glb` somewhere in the project and add it to `MODELS` and the buttons.
- Combine with image tracking: grab the Eiffel Tower that appears on the picture.

## Troubleshooting

- **Hand not detected:** show your whole hand, palm toward the camera, in good light, 30–80 cm away.
- **Pinch doesn't grab:** start the pinch *on* the model (inside its outline), then move.
- **Hand signs trigger by accident:** they need 0.4 s; raise it in the code (`now - sign.since > 400`).
- **Slow:** close other tabs; Chrome is usually smoother than Safari.
