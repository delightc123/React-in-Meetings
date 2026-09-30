# React in Meetings — Chronological Session & Sprint Log

> **Domain Scope**: Chronological log of sprint sessions, feature implementations, refactoring, and milestone completions.

---

### [2026-09-30] — Session 1: Core Vision Pipeline, Dual Runtime Engine & Landmark Architecture
- **Author / Owner**: Delight Chukwu
- **Changes Completed**:
  - Implemented real-time MediaPipe Tasks inference pipeline integrating Face Landmarker (478 mesh points + 52 blendshapes), Hand Landmarker (21 keypoints), and Pose Landmarker Lite (33 body landmarks).
  - Engineered dual runtime execution model:
    - [`react_in_meetings_v2.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings_v2.py): Dynamic standard deviation (sigma) scoring calibrated against individual resting neutral facial baselines.
    - [`react_in_meetings.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings.py): Fixed-threshold fallback engine for zero-setup verification.
  - Implemented scale-invariant spatial math (`near(pt_a, pt_b, ratio)`) dividing all spatial landmark distances by face bounding box width, guaranteeing identical sensitivity across distances.
  - Built multi-frame hysteresis debounce state machine (`ARM` and `HOLD_FRAMES`) to eliminate micro-gesture flickering and speech-induced false positives.
  - Scaffolded progressive rule set in `/.rules/`:
    - `vision-pipeline.rules.md`
    - `gesture-rules.rules.md`
    - `compositor.rules.md`
    - `virtual-camera.rules.md`
  - Established root [`AGENTS.md`](file:///c:/Software/PROJ/React-in-Meetings/AGENTS.md) orchestration system and global invariants.
  - Initialized flat progressive disclosure memory system at root [`memory.md`](file:///c:/Software/PROJ/React-in-Meetings/memory.md) and `/.memory/`.

---

### [2026-09-30] — Session 2: Compositor Engine, Virtual Camera Loop & Executive Documentation
- **Author / Owner**: Delight Chukwu
- **Changes Completed**:
  - Engineered head-tracked alpha compositor supporting JPEGs, transparent PNGs, and multi-frame animated GIFs with intrinsic frame duration pacing.
  - Integrated `pyvirtualcam` backend with OBS loopback device publishing, providing automatic fail-safe fallback to OpenCV local preview when virtual drivers are uninitialized.
  - Authored comprehensive [`README.md`](file:///c:/Software/PROJ/React-in-Meetings/README.md) covering installation, virtual camera routing across Zoom/Google Meet/Teams/Discord, 14 gesture trigger mechanics, and mathematical sigma formulations.
  - Configured repository [`.gitignore`](file:///c:/Software/PROJ/React-in-Meetings/.gitignore) isolating user-specific calibration data (`calibration.json`), virtual environments, and bytecode caches.
