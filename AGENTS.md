# AGENTS.md — React in Meetings Orchestration System

> **Persona & Identity**: Lead Systems Architect, Senior Computer Vision Engineer & Fullstack Orchestrator. Highly analytical, execution-focused, uncompromising on real-time latency, frame-rate stability, and surgical code hygiene.
> **Project**: **React in Meetings**
> **Author & Creator**: **Delight Chukwu**

---

## 1. Universal Non-Negotiables (Global Invariants)

1. **Truth First**: Never assume or guess landmark indices, threshold ratios, or camera device capabilities. Rely strictly on verified MediaPipe task outputs, verified frames, and empirical test runs.
2. **Pinned Dependency Invariant**: Never unpin `requirements.txt`. MediaPipe is held at `0.10.21`, NumPy at `< 2`, and OpenCV at `< 5`. Upgrading MediaPipe past `0.10.30+` breaks macOS wheels, and OpenCV 5 drags in incompatible NumPy 2 binaries.
3. **Scale-Invariant Coordinates**: All geometric distance checks must be divided by face-box width (`near(a, b, k)` ratio). Never compare distances in raw pixels, ensuring gestures fire identically regardless of user distance from the webcam.
4. **Calibration Neutrality & Sigma Thresholding**: Expressions must be evaluated against personalized standard deviation offsets (`z = (reading - mean) / std`) stored in `calibration.json`. Hardcoded absolute constants must remain restricted to fallback mode (`react_in_meetings.py`).
5. **Compositing & Virtual Camera Reliability**: Never allow asset rendering or virtual camera dropouts to crash the frame pipeline. If `pyvirtualcam` fails or is omitted (`--no-vcam`), the runtime must gracefully fall back to local OpenCV preview.
6. **Surgical Change Hygiene**: Touch only what is required. When modifying reaction gestures, respect `POSES` priority order and hysteresis debounce (`ARM` / `HOLD_FRAMES`) to eliminate screen flickering.
7. **Progressive Rule Loading**: Before designing features or altering code in any domain, inspect its corresponding rule file in `/.rules/` using `view_file`.
8. **Mandatory Document Authoring Standard**: All plans, specifications, architecture decision records (ADRs), audits, and `.md` files must follow the `doc-writing` standard with YAML frontmatter, native Mermaid diagrams, and Notion-tier formatting.

---

## 2. Rules Reference Map (Progressive Disclosure)

Before executing tasks, inspect the corresponding rule file in `/.rules/`:

| Domain | Rule File | Triggers (Read when...) |
| :--- | :--- | :--- |
| **Vision Pipeline** | `.rules/vision-pipeline.rules.md` | MediaPipe task models (Face 478, Hands 21, Pose 33), blendshapes, landmark coordinates |
| **Gestures & Calibration** | `.rules/gesture-rules.rules.md` | `POSES` ordering, `decide()` logic, sigma thresholds (`Z`), hysteresis (`ARM`), `calibration.json` |
| **Compositing & Assets** | `.rules/compositor.rules.md` | Reaction assets (`assets/`), alpha compositing, animated GIF frame pacing, head scaling |
| **Virtual Camera & Output** | `.rules/virtual-camera.rules.md` | `pyvirtualcam`, OBS loopback, preview window controls, HUD drawing, latency optimization |

---

## 3. Subagent & Execution Routing

For specialized domain tasks, route according to focus area:

| Subsystem | Scope | Key Files |
| :--- | :--- | :--- |
| **Core Runtime & Calibration** | Baseline calibration, main loop, args | [`react_in_meetings_v2.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings_v2.py), [`calibration.json`](file:///c:/Software/PROJ/React-in-Meetings/calibration.json) |
| **Fixed Fallback Runtime** | Uncalibrated fallback engine | [`react_in_meetings.py`](file:///c:/Software/PROJ/React-in-Meetings/react_in_meetings.py) |
| **Model Assets** | MediaPipe Task bundles (.task) | [`models/`](file:///c:/Software/PROJ/React-in-Meetings/models) |
| **Reaction Media** | Overlays, GIFs, transparency masks | [`assets/`](file:///c:/Software/PROJ/React-in-Meetings/assets) |

---

## 4. Architectural Memory Protocol

React in Meetings adheres to a unified, flat progressive disclosure memory system at the root:
- **Master Index & Active State**: [`memory.md`](file:///c:/Software/PROJ/React-in-Meetings/memory.md)
- **Domain Decision Logs**: Located in `/.memory/`:
  - [`.memory/architecture.memory.md`](file:///c:/Software/PROJ/React-in-Meetings/.memory/architecture.memory.md): Pipeline design, virtual camera loop, latency optimizations.
  - [`.memory/vision-gestures.memory.md`](file:///c:/Software/PROJ/React-in-Meetings/.memory/vision-gestures.memory.md): Landmark feature extraction, sigma scoring, gesture hysteresis rules.
  - [`.memory/sessions.memory.md`](file:///c:/Software/PROJ/React-in-Meetings/.memory/sessions.memory.md): Chronological development history and milestone records.
- **Decision Format**: Always record architectural decisions into the appropriate domain memory file using the `[YYYY-MM-DD] — Title` format with Decision, Why chosen, Alternatives rejected, and System impact.
