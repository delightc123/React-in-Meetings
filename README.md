# React in Meetings

Pull a face at your webcam. It works out *which* face, and drops the matching meme over your head, scaled to follow you around the frame. You can extend and add more memes to your heart's desire.

Point Zoom at its virtual camera and the whole call sees it.

```bash
python react_in_meetings.py              # preview + virtual camera
python react_in_meetings.py --no-vcam    # preview only
```

Fourteen reactions out of the box: **time out**, **heart hands**, **hands over face**, **crashing out**, **dancing**, **nose pinch**, **flirty**, **hand up**, **tongue out**, **gasp**, **disgust**, **talking to the wall**, **side-eye**, and **spinning**.

There is a second file, `react_in_meetings_v2.py`, which is the same engine with expression thresholds calibrated to **your** face instead of a guessed constant.

---

## Setup

```bash
# Create and activate virtual environment
python3.12 -m venv venv
source venv/bin/activate           # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

> [!NOTE]
> Requires **Python 3.11** or **3.12**. Three MediaPipe task bundles (~15 MB) download automatically on first run into `models/`.

> [!WARNING]
> **Don't unpin the dependencies.** MediaPipe `0.10.30+` (including `1.0.x`) ships macOS wheels that abort the moment they open a detector, so it is pinned at `0.10.21`. That build requires NumPy 1.x, and OpenCV 5 requires NumPy 2 — while MediaPipe `0.10.21` asks for an *unpinned* `opencv-contrib-python`, which quietly drags OpenCV 5 and NumPy 2 back in. That is why the OpenCV pins exist. Unpin one and you have to unpin all three.

---

## Running It

```bash
# Calibrate your resting face once (takes 7 seconds)
python react_in_meetings_v2.py --calibrate

# Launch the live engine
python react_in_meetings_v2.py
```

### Keyboard Controls

| Key | Action |
| :--- | :--- |
| `q` | Quit application |
| `d` | Toggle the diagnostic HUD (shows real-time blendshapes & sigmas) |
| `c` | Recalibrate resting baseline interactively |
| `1`–`9`, `0`, `-`, `=`, `[`, `]` | Force-trigger a specific reaction on screen for 2 seconds |

---

## Using It in Meetings

The virtual camera is enabled by default. Zoom, Google Meet, Microsoft Teams, Discord, and OBS all treat it as a standard webcam feed.

### 1. Install a Backend (Once)

| Operating System | Setup Step |
| :--- | :--- |
| **macOS** | Install [OBS Studio](https://obsproject.com), open it once to register the virtual device, then quit. |
| **Windows** | Install OBS Studio, or run its virtual camera installer utility. |
| **Linux** | Run `sudo apt install v4l2loopback-dkms` followed by `sudo modprobe v4l2loopback`. |

### 2. Launch the App

The console prints the target device it publishes to:

```text
Virtual camera: 'OBS Virtual Camera'  <- select this camera in Zoom / Meet
```

### 3. Select the Device in Your Video App

- **Zoom**: `Settings` → `Video` → `Camera` → Select `OBS Virtual Camera`.
- **Google Meet / Teams / Discord**: Look under video device settings.

> [!IMPORTANT]
> **Start React in Meetings before launching your meeting app.** Most meeting clients scan for connected cameras once on launch and won't detect virtual devices registered afterwards.

> [!TIP]
> Test this on a call with a friendly colleague first! The engine triggers autonomously based on your actual facial expressions, so everyone sees whatever gesture it detects.

---

## The Reactions

| Pose | Trigger Gesture |
| :--- | :--- |
| `time_out` | Referee's T — one hand flat horizontally on top, one vertical hand underneath |
| `heart` | Two hands forming a heart — index fingertips together, thumb tips together |
| `cover_nose` | Both hands covering your nose and mouth |
| `crashing_out` | Both hands clutching your head with mouth wide open |
| `dance` | Both hands up behind your head with mouth closed |
| `nose_closed` | Pinch your nose shut |
| `flirty` | One index fingertip resting on your lips |
| `hand_up` | One open palm raised beside your head |
| `tongue_out` | Tongue extended, mouth open |
| `open_mouth` | Dramatic jaw drop / gasp |
| `disgusted` | Scrunch your nose, or brows pulled down into a frown |
| `talking_to_wall` | Hands in frame, gesturing away from camera |
| `suspicious` | Turn your head sideways and squint |
| `spin` | Leave the webcam frame entirely |

Reaction media lives in `assets/`, named after the corresponding pose (e.g. `heart.jpeg`, `spin.gif`). Swap in your own by dropping a file with the matching name. JPEG, PNG, and animated GIFs all work seamlessly: alpha transparency composites cleanly, and GIF frame timings are read directly from the file. If an asset is missing, you get a clean red placeholder box rather than a runtime crash.

---

## Customizing & Extending

### Swapping a Meme (~30 seconds)

Drop a file into `assets/` named after the pose — `heart.png` replaces the default heart reaction.
- JPEG, PNG, and animated GIFs are supported.
- Transparency channels composite automatically.
- Prefixes like `2026_heart.png` are accepted.
- Press that pose's test key (`1`–`=`) to verify how it sits over your head.

### Adding a New Pose

1. **Place Asset**: Save your image/GIF in `assets/` (e.g., `assets/thinking.png`).
2. **Register Pose**: Append the identifier to `POSES` in `react_in_meetings_v2.py`. The list evaluates top-to-bottom, so place specific poses above generic ones.
3. **Add Detection Logic**: Insert a branch in `decide()`:
   ```python
   for h in hands:
       if near(h.palm, face.chin, 0.5) and not h.open:
           return "thinking", d
   ```
   *Available features*:
   - `face`: `.nose`, `.chin`, `.mouth`, `.w`, `.h`, `.b("jawOpen")` for any of the 52 blendshapes.
   - `hands`: `.palm`, `.thumb`, `.index`, `.open`.
   - `body`: `.elbows_up`.
   - `m`: Normalized facial expressions measured in standard deviations (sigma).
   - `near(a, b, k)`: Evaluates whether distance is within `k` face-widths (maintains distance invariance).
4. **Tune Stability**: Assign an `ARM` count if the gesture is twitchy, then calibrate against the HUD (`d` key).

> [!CAUTION]
> `TEST_KEYS` assigns one test key per pose by index position. Adding a 15th pose works fine without a key; removing a pose without trimming `TEST_KEYS` will raise an IndexError when pressed.

---

## How It Works

```text
webcam frame
     │
 1. MediaPipe Tasks    face: 478 landmarks + 52 blendshapes
                       hands: 2 × 21 points
                       body: shoulders, elbows, wrists
     │
 2. Feature Extraction face-relative geometry, tongue color, hand velocity
     │
 3. Baseline Normalization expressions converted to sigma (Z) above YOUR resting face
     │
 4. decide() Engine    ordered rules pass — first matching pose fires
     │
 5. Hysteresis (ARM)   persists N frames to activate, lingers 10 frames after
     │
 Composite Output      scaled to face bounding box, alpha-blended, streamed to vcam
```

### Normalizing Away Camera Distance

Landmark coordinates natively emerge in pixel units, which fluctuate depending on sitting distance from the camera. To achieve scale invariance, all geometric measurements are divided by the face bounding box width:

$$\text{Distance Ratio} = \frac{\|\mathbf{p}_1 - \mathbf{p}_2\|}{\text{Face Width}}$$

`near(hand.index, face.mouth, 0.22)` evaluates to "within 22% of a face width," ensuring identical sensitivity whether you sit 30 cm or 2 meters from the lens. Hand velocity and head yaw are normalized using the same geometric principle.

### Why Fixed Thresholds Fail (and Why Sigma Calibration Wins)

MediaPipe blendshape values are **never zero when your face is at rest**, and resting baselines differ across individuals:
- Some faces idle at `jawOpen = 0.02`; others sit naturally at `0.19`.
- A resting smile can read `mouthSmile = 0.30` while thinking about nothing.

Consequently, hardcoded thresholds like `jawOpen > 0.5` are physically unreachable for some faces and overly sensitive for others.

`react_in_meetings_v2.py` resolves this through a 7-second resting face calibration. It computes the **mean ($\mu$) and standard deviation ($\sigma$)** across all 52 blendshape channels:

$$z = \frac{\text{reading} - \mu_{\text{neutral}}}{\sigma_{\text{neutral}}}$$

"6 sigma above your resting jaw" produces an identical, reliable trigger across any face:

```text
                       Still Face        Mobile Face
Resting Neutral        z = +0.1          z = +0.1         (Quiet)
Gasp Expression        z = +38.7         z = +8.7         (Both trigger reliably)
Nose Scrunch           z = +19.3         z = +5.5         (Both trigger reliably)
```

---

## Project Structure

```text
React-in-Meetings/
├── react_in_meetings_v2.py   # Primary calibrated production runtime (recommended)
├── react_in_meetings.py      # Fixed-threshold fallback runtime
├── calibration.json          # Personalized neutral baseline (generated via --calibrate)
├── requirements.txt          # Strictly pinned dependencies (do not alter)
├── assets/                   # Reaction media library (images & animated GIFs)
├── models/                   # MediaPipe .task bundles (auto-downloaded)
├── .rules/                   # Subsystem architecture rules
└── .memory/                  # Progressive disclosure decision & session logs
```

---

## Author & Maintainer

**Delight Chukwu**  
*React in Meetings Engine*
