# Vision Pipeline Rules

> **Domain Scope**: Covers MediaPipe task model initialization (`FaceLandmarker`, `HandLandmarker`, `PoseLandmarker`), landmark extraction, and scale-free coordinate transformations.

---

## 1. Model Lifecycle & Initialization
- MediaPipe task models are loaded from `models/`:
  - `models/face_landmarker.task`
  - `models/hand_landmarker.task`
  - `models/pose_landmarker_lite.task`
- Always verify model presence before instantiation; use auto-download fallback via `urllib.request` if absent.
- Run detectors in `RunningMode.VIDEO` mode with monotonic microsecond timestamps (`mp.Timestamp.from_seconds(...)`).

## 2. Scale-Free Coordinate Geometry
- Raw MediaPipe landmarks are returned in normalized pixel space (`[0.0, 1.0]`).
- Scale all geometric distances by the detected face bounding box width:
  ```python
  def near(pt_a, pt_b, threshold_in_face_widths):
      ...
  ```
- Head yaw is determined by the nose's relative horizontal position between the left and right face boundaries:
  - `0.0`: Facing camera directly
  - `~0.4`: Full profile turn

## 3. Blendshape Extraction & Integrity
- Face mesh supplies 52 facial blendshapes (e.g. `jawOpen`, `mouthSmileLeft`, `mouthSmileRight`, `eyeBlinkLeft`, `eyeWideRight`, `browDownLeft`).
- Blendshape values range from `0.0` to `1.0`. Always verify baseline offsets rather than assuming `0.0` at rest.
