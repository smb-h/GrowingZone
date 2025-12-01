# GrowingZone

GrowingZone is a small interactive demo of **region growing image segmentation** implemented in plain HTML, CSS, and JavaScript. It lets you load an image, pick seed pixels, and visualize how regions expand based on an intensity threshold, with a smooth animated “glow” effect.

---

## Features

- 🖼 **Image loading** from your local machine (no uploads, everything stays in the browser)
- 🧬 **Seed-based region growing**: click on any pixel to use it as the starting point
- 🎚 **Intensity threshold control (0–255)** to tune how strict/loose the region similarity is
- ✨ **Animated expansion** of the region using `requestAnimationFrame`
- 🌈 **Colored overlays** for each new region, with a soft glow near the seed
- 🔁 **Multiple regions**: run region growing multiple times with different seeds/thresholds
- ♻️ **Reset** button to restore the original image at any time
- 🕶 **Dark, fixed-layout UI** with no scrollbars (fits the viewport)

---

## How it works

### Region growing logic

The core idea is **seeded region growing** in grayscale:

1. When you click on the image, that pixel is the **seed**.
2. Its intensity (converted to grayscale) is used as the **reference**.
3. For each visited pixel, its 4-neighbors (up, down, left, right) are checked:
   - Compute grayscale value for the neighbor.
   - If `|gray_neighbor - gray_seed| <= threshold`, that neighbor is added to the region.
4. The process repeats in a breadth-first manner until no more pixels satisfy the condition.

The grayscale value is computed from RGB using a standard luminance approximation:

```js
0.299 * R + 0.587 * G + 0.114 * B
````

which maps each pixel into a single intensity in `[0, 255]`.

### Threshold (0–255)

The **Threshold** slider directly represents the **maximum allowed difference in gray level** between the seed pixel and any pixel in the region:

* `0`  → only pixels with identical gray level as the seed are accepted.
* `20` → pixels whose intensity is within 20 gray levels of the seed are accepted.
* `255` → all pixels are accepted (the region can grow across the whole image).

Larger thresholds produce **bigger, looser regions**; smaller thresholds produce **tighter, more homogeneous regions**.

### Animation & glow

Instead of coloring the whole region in one step, GrowingZone:

* Uses a **queue** (BFS) and processes a fixed **batch** of pixels per animation frame.
* On each frame:

  * Dequeues up to `batchSize` pixels.
  * Colors them with a semi-transparent overlay.
  * Schedules the next frame with `requestAnimationFrame`.

To create a subtle **glow** around the seed:

* The distance from the current pixel to the seed is computed.
* A **glow factor** is derived from this distance.
* Pixels closer to the seed are blended with a **higher alpha** (stronger color), and pixels farther away get a softer overlay.

This produces a region that appears to **expand outward like a glowing wave**.

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 👤 Author

**SMB H**
[https://smb-h.com](https://smb-h.com)
