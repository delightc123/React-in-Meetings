# React in Meetings — Architecture Decision Log

> **Domain Scope**: Covers high-level system architecture, frame capture loop, MediaPipe multi-task inference pipeline, virtual camera routing, and latency guarantees.

---

### [2026-09-30] — Dual Runtime Architecture (Calibrated v2 vs Fixed v1)

**Decision**:
Maintained two distinct operational entrypoints:
1. [`react_in_meetings_v2.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings_v2.py): Default production engine utilizing dynamic z-score sigma thresholds calibrated to each user's unique neutral face.
2. [`react_in_meetings.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings.py): Fallback engine utilizing fixed scalar thresholds for environments where user calibration cannot be performed.

**Why chosen**:
- Facial blendshape baselines vary widely across individuals (e.g. resting mouth smile ranges from 0.02 to 0.25). A fixed threshold either fails to trigger for subtle expressions or constantly fires false positives.
- Preserving the uncalibrated v1 script ensures zero-setup immediate testing and automated pipeline verification without requiring interactive 7-second user calibration.

**Alternatives rejected**:
- *Unified binary with mandatory calibration prompt on every startup*: Rejected because it introduces friction during rapid restarts and virtual camera toggling.
- *Discarding v1 entirely*: Rejected because having a deterministic scalar baseline aids regression testing when tuning MediaPipe model upgrades.

**System Impact**:
- Users run `react_in_meetings_v2.py --calibrate` once, saving baselines to `calibration.json`. The runtime immediately loads existing baselines on subsequent launches.

---

### [2026-09-30] — Pinned Dependency Stack Invariant

**Decision**:
Enforced strict dependency pinning in [`requirements.txt`](file:///c:/Software/PROJ/React-in-Meetings/requirements.txt):
- `mediapipe==0.10.21`
- `numpy<2`
- `opencv-python<5`
- `opencv-contrib-python<5`
- `pyvirtualcam`
- `pillow`

**Why chosen**:
- MediaPipe releases >= 0.10.30 contain breaking issues on macOS and C++ binary aborts when opening detectors.
- MediaPipe 0.10.21 strictly depends on NumPy 1.x.
- OpenCV 5 requires NumPy 2. Installing unpinned OpenCV or unpinned `opencv-contrib-python` pulls NumPy 2, instantly corrupting the MediaPipe runtime.

**Alternatives rejected**:
- *Unpinning dependencies to latest versions*: Caused immediate runtime aborts.
- *Containerizing the runtime via Docker*: Added unnecessary latency and complex webcam / GPU passthrough overhead on developer workstations.

**System Impact**:
- Guaranteed reliable cross-platform execution on Windows, macOS, and Linux.
