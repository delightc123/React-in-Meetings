# Virtual Camera & Output Rules

> **Domain Scope**: Covers `pyvirtualcam` backend streaming, OBS loopback, latency optimization, and graceful fallbacks.

---

## 1. Virtual Camera Lifecycle
- Primary driver: `pyvirtualcam.Camera(width=W, height=H, fps=fps, fmt=pyvirtualcam.PixelFormat.BGR)`.
- If device drivers are missing or initialization raises an exception:
  - Print diagnostic guidance (e.g. install OBS Virtual Camera).
  - Automatically fall back to preview-only mode (`vcam = None`) without crashing.
- Support `--no-vcam` CLI flag for headless testing or local preview development.

## 2. Real-Time Latency Invariant
- Never introduce blocking file I/O or synchronous network calls within the per-frame processing loop.
- Keep per-frame inference, compositing, and output pipeline strictly under 33ms (target 30+ FPS).
- Maintain HUD telemetry toggled by `d` key to track actual runtime FPS and decision latency.
