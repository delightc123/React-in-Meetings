# Gesture Rules & Decision Engine

> **Domain Scope**: Covers gesture evaluation ordering in `decide()`, sigma calculations against `calibration.json`, hysteresis stabilization (`ARM`), and reaction triggers.

---

## 1. Decision Priority Order
- The `decide()` function runs an ordered sequence of checks. The first matching rule immediately returns.
- More specific/restrictive multi-condition gestures (e.g. `heart`, `crashing_out`, `dance`) must appear earlier in `decide()` than generic gestures (e.g. `open_mouth`, `flirty`).
- Never append a new pose without checking its interaction with existing poses in `POSES`.

## 2. Dynamic Sigma Thresholding (v2)
- In `react_in_meetings_v2.py`, expressions are measured as z-scores above the user's neutral baseline:
  ```python
  z = (current_value - neutral_mean) / neutral_std
  ```
- Use floor clipping (`max(std, MIN_STD)`) to prevent division by near-zero jitter on motionless faces.
- Calibration captures 7 seconds of neutral resting expressions to populate `calibration.json`.

## 3. Hysteresis & Debouncing
- `ARM[pose]`: Frame count that a gesture must consistently hold before firing. Prevents momentary twitches from false-triggering reactions.
- `HOLD_FRAMES`: Frame duration that a reaction remains displayed on screen after the trigger ceases, preventing rapid on-off flicker.
