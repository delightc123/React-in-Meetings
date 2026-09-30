# React in Meetings — Vision & Gestures Decision Log

> **Domain Scope**: Covers MediaPipe 478 face landmarks, 21 hand landmarks, 33 body pose landmarks, blendshape extraction, sigma scoring mathematics, and gesture priority hierarchies.

---

### [2026-09-30] — Scale-Invariant Geometry & Relative Measurements

**Decision**:
All Euclidean spatial distance comparisons between landmarks (e.g. index fingertip to mouth, hand to nose) are divided by the detected face bounding box width (`near(pt_a, pt_b, k)` where `k` is a fraction of face width).

**Why chosen**:
- Pixel distances change drastically when the user leans forward or backward relative to the webcam.
- Normalizing against facial width makes all 14 gesture triggers invariant to distance from the lens (from 30 cm to 2 meters).

**Alternatives rejected**:
- *Absolute pixel distance thresholds*: Fragile; triggers break whenever the user shifts position in their chair.
- *Full 3D world landmark depth (Z)*: MediaPipe pseudo-depth estimates are noisy and fluctuate under variable office lighting.

**System Impact**:
- Consistent gesture triggering across varying camera focal lengths and seating distances.

---

### [2026-09-30] — Multi-Frame Hysteresis Debounce (ARM & HOLD_FRAMES)

**Decision**:
Implemented a two-tier stabilization state machine:
1. `ARM[pose]`: Requires candidate reaction to match continuously for N frames (e.g. 4–15 frames depending on gesture sensitivity) before activating the overlay.
2. `HOLD_FRAMES` (10 frames): Retains active overlay for 10 frames after gesture conditions drop below threshold.

**Why chosen**:
- Human expressions involve micro-movements and speech-related facial oscillations. Without `ARM`, speech patterns trigger fleeting false positives.
- Without `HOLD_FRAMES`, minor landmark occlusion or boundary flutter causes intense, distracting strobe/flicker in video calls.

**Alternatives rejected**:
- *Single-frame instant trigger*: Causes extreme visual flickering and false positives.
- *Exponential Moving Average (EMA) on all 52 blendshapes*: Added latency and sluggish gesture release without preventing priority misclassifications.

**System Impact**:
- Smooth, professional meme transitions suitable for live business meetings.
