# Compositor & Asset Rendering Rules

> **Domain Scope**: Covers reaction asset management (`assets/`), alpha compositing over webcam frames, animated GIF decoding, and head-tracked bounding box scaling.

---

## 1. Asset Conventions
- Reaction media resides in `assets/`, keyed by pose name (`<pose>.png`, `<pose>.jpeg`, `<pose>.gif`).
- Transparent PNGs and RGBA GIFs must have their alpha channels properly unmultiplied and blended over the camera BGR frame.
- If an asset file is missing, render a visual red bounding box placeholder instead of terminating the runtime loop.

## 2. Head-Tracked Scaling & Alignment
- Reaction overlays are anchored relative to the face box center:
  ```python
  overlay_w = int(face.w * FACE_SCALE)
  overlay_h = int(face.h * FACE_SCALE)
  ```
- Bounding box clamp: When the user moves near frame edges, clamp overlay coordinates within `[0, frame_width]` and `[0, frame_height]` to prevent out-of-bounds slice exceptions.

## 3. Animated GIF Pacing
- GIF frames must read intrinsic per-frame delay timings from image metadata.
- Advance frames monotonically based on real elapsed time rather than per-frame iteration counter.
