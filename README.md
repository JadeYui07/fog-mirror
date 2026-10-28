# Fog Mirror

**Yuzhuo's playground experiment**

Fog Mirror turns your webcam into a full-screen condensation mirror. Point at the glass to make a precise wipe, open your palm to clear a broad area, or open your mouth to let fog bloom back over the reflection.

The experience lives in one file: [`fog.html`](./fog.html). There is no build step and no microphone access.

## Run it locally

Camera access requires HTTPS or localhost. Opening the file directly with `file://` may prevent the browser from using the webcam.

```bash
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/fog.html
```

Chrome or Edge is recommended. Allow camera access when the browser asks. The first launch also needs a network connection so the MediaPipe hand and face models can load from jsDelivr.

## How to use the mirror

| Action | Gesture | Result |
| --- | --- | --- |
| Fine wipe | Extend your index finger and curl the other three fingers | Clears a narrow, controlled path at the fingertip |
| Broad wipe | Open all five fingers | Clears a soft area sized from the visible width of your palm |
| Alternate fine wipe | Pinch your thumb and index finger | Clears a narrow path between the two fingertips |
| Add fog | Open your mouth briefly | Blooms a large, feathered patch of condensation around the mouth |
| Pointer fallback | Drag with a mouse, trackpad, or touch | Clears the fog without a camera |

The open-palm gesture waits for two consecutive detections before it starts wiping. A closed fist does nothing, which helps separate the broad wipe from the single-finger gesture.

## Live controls

Open **Controls** in the upper-right corner to tune the effect while it is running.

| Control | Default | What it changes |
| --- | ---: | --- |
| Fog Density | 20% | Opacity of the milky condensation layer |
| Mirror Blur | 23 px | Softness of the reflection behind the fog |
| Droplet Density | 7% | Amount of fine condensation texture |
| Wipe Radius | 15 px | Radius used by pointer, touch, pointing, and pinching |
| Wipe Softness | 51% | Feathering around a wiped path |
| Refog Speed | 11% | Rate at which wiped areas gradually fog over again |
| Pinch Sensitivity | 62% | How tightly the thumb and index finger must meet |
| Motion Smoothing | 60% | Balance between stable motion and gesture latency |
| Breath Radius | 62% | Size of a new fog bloom |
| Breath Density | 39% | Visibility of freshly added fog |

Select **Reset Fog** to restore an even layer of condensation.

## Privacy

- Camera frames are processed locally in the browser.
- The page does not request microphone access.
- Open-mouth detection simulates breathing through visual face landmarks.
- No image or gesture data is uploaded by the page.
- MediaPipe assets and the Satoshi webfont are loaded from external CDNs.

## How it works

The mirror is rendered with layered HTML5 canvases:

1. The webcam is drawn as a horizontally mirrored full-screen scene.
2. A blurred reflection, haze, condensation texture, and droplets form the fog layer.
3. A separate mask records wiped regions.
4. MediaPipe Hands classifies pointing, open-palm, and pinching gestures.
5. MediaPipe Face Mesh detects mouth opening and adds soft radial fog blooms.
6. Hand landmarks update target positions while the brush moves and draws on every display frame for smoother motion.

The hand and face models run in a shared serial inference queue. This avoids asking both models to use the browser's inference resources at the same time.

## Troubleshooting

### The camera does not start

- Serve the page from localhost or HTTPS.
- Confirm that camera permission is allowed for the site.
- Close other applications that may already be using the camera.

### Hand tracking keeps retrying

- Confirm that jsDelivr is reachable from your network.
- Use a current version of Chrome or Edge.
- Keep one hand clearly visible with steady front lighting.
- Open the browser console and look for `MediaPipe Hands inference failed:` for the underlying error.

### Gestures feel delayed

- Lower **Motion Smoothing** slightly.
- Use even lighting and keep the full hand inside the camera frame.
- A stable camera frame rate is more useful than requesting a high frame rate that the device cannot sustain.

### The broad wipe does not activate

- Show the palm to the camera.
- Separate the thumb from the index finger.
- Keep all five fingers visible for at least two tracking frames.

## Project structure

```text
fog.html   # Complete interactive experience
README.md  # This guide
```

Designed and built by **Yuzhuo Zhang** as a playground experiment.
