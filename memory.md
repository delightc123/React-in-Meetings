# React in Meetings — Architecture Memory & System Index

> **Purpose**: This repository utilizes a unified **Progressive Disclosure** memory system. This root file provides an active system architecture snapshot and routes to comprehensive domain decision logs in `/.memory/`.
> **Owner & Creator**: **Delight Chukwu**

---

## 1. Active System Architecture (Current State)

- **Core Engine**: Real-time webcam facial gesture & body pose detection application with automated head-tracked meme overlay compositor.
- **Technology Stack**: Python 3.11 / 3.12, MediaPipe Tasks `0.10.21` (Vision models: Face Landmarker 478 pts, Hand Landmarker 21 pts, Pose Landmarker 33 pts), OpenCV `< 5`, NumPy `< 2`, Pillow, and `pyvirtualcam`.
- **Primary Runtime Modes**:
  - `react_in_meetings_v2.py`: Production runtime utilizing personalized neutral calibration (`calibration.json`) and dynamic standard deviation (sigma) scoring.
  - `react_in_meetings.py`: Fixed-threshold fallback engine for uncalibrated environments.
- **Output Targets**: Local OpenCV preview window and virtual camera broadcast (OBS Virtual Camera loopback, Zoom, Microsoft Teams, Google Meet, Discord).
- **Critical System Invariants**:
  1. **Strict Dependency Pinning**: MediaPipe held at `0.10.21`, NumPy `< 2`, OpenCV `< 5`. Never unpin or install incompatible wheels.
  2. **Scale-Invariant Math**: Landmark coordinates and gesture triggers must scale relative to face bounding box width (`near()`), never raw pixel distances.
  3. **Zero Crash Fallback**: Virtual camera initialization failures must gracefully degrade to local preview (`--no-vcam`). Missing media assets render placeholder indicators without terminating frame pipeline.
  4. **Hysteresis Debounce**: All reactions use multi-frame arming (`ARM`) and release retention (`HOLD_FRAMES`) to eliminate UI flicker.

---

## 2. Memory Reference Map

Before modifying shared pipeline logic or querying architectural rationale, inspect the corresponding domain decision log via `view_file`:

| Domain | Memory File | Triggers (Read when...) |
| :--- | :--- | :--- |
| **Architecture & Core** | `.memory/architecture.memory.md` | Dual runtime design, pipeline loop, virtual camera streaming, latency |
| **Vision & Gestures** | `.memory/vision-gestures.memory.md` | MediaPipe tasks, blendshapes, sigma calibration, gesture ordering, ARM |
| **Session History** | `.memory/sessions.memory.md` | Chronological sprint records, refactoring logs, verification gates |

---

## 3. Active Sprint & Recent Milestones

- **[2026-09-30]**: Compositor Engine, Virtual Camera Loop & Executive Documentation (Sprint 2):
  - Authored comprehensive [`README.md`](file:///c:/Software/PROJ/React-in-Meetings/README.md) with modern typography, meeting setup tables, and mathematical sigma formulations without metadata frontmatter.
  - Engineered head-tracked alpha compositor supporting JPEGs, PNGs, and animated GIFs with automatic bounding-box scaling and clamp protection.
  - Configured repository [`.gitignore`](file:///c:/Software/PROJ/React-in-Meetings/.gitignore) to isolate calibration baselines, virtual environments, and bytecode caches.
- **[2026-09-30]**: Core Vision Pipeline, Dual Runtime Engine & Landmark Architecture (Sprint 1):
  - Engineered dual runtime execution model: calibrated production engine ([`react_in_meetings_v2.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings_v2.py)) and fixed-threshold fallback engine ([`react_in_meetings.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings.py)).
  - Implemented scale-invariant spatial math (`near()`) based on facial bounding box width ratios, eliminating camera distance sensitivity.
  - Built multi-frame hysteresis state machine (`ARM` and `HOLD_FRAMES`) for 14 reaction states to eliminate UI flickering.
  - Established progressive disclosure rules in `/.rules/` and orchestration system [`AGENTS.md`](file:///c:/Software/PROJ/React-in-Meetings/AGENTS.md).
  - Initialized flat progressive disclosure memory system at [`memory.md`](file:///c:/Software/PROJ/React-in-Meetings/memory.md) and `/.memory/`.
*(Full chronological history logged in [`.memory/sessions.memory.md`](file:///c:/Software/PROJ/React-in-Meetings/.memory/sessions.memory.md))*

---

## 4. Memory Maintenance Protocol

1. **Architectural Decisions**: When a permanent design choice or constraint is established, append to the appropriate domain file in `.memory/` using the `[YYYY-MM-DD] — Title` format with Decision, Why chosen, and Alternatives rejected.
2. **Session Logs**: Conclude tasks by appending to `.memory/sessions.memory.md` and bumping the top milestone in Section 3 above.
3. **Atomic Scoping**: Never bloat this root index file. Keep it strictly under 150 lines.
