# SAM2 Masking Tool

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/opsabove)

Mask generator powered by Meta's **SAM2**, built for two workflows:

- **LichtFeld Studio** — 3D Gaussian Splatting training
- **WebODM** — photogrammetry (point clouds, meshes, orthophotos)

Click an object on one frame and the tool tracks it through every frame of your dataset,
then writes masks in the exact format each of these tools expects.

---

## How it works

1. You place a few clicks on a frame — left-click marks the object, right-click marks background.
2. SAM2 treats your image folder as a video and **tracks the object forward and backward** from the clicked frame(s), so every image gets a mask.
3. Each mask is cleaned up — tiny speckle holes are filled and the border is optionally trimmed (**Edge Shrink**) so unstable edge pixels don't turn into spikes in your splat.
4. Masks are saved next to your dataset in the format your trainer expects.

---

## Features

- **Two tasks**
  - **Keep object** — isolate one subject (person, car, statue); the background is removed.
  - **Remove ghosts** — mark passers-by or moving objects; they are excluded and the rest of the scene is reconstructed.
- **Multiple objects** — "+ New object" tracks each person or item separately and merges them into one mask.
- **Prompts on any frame** — add clicks wherever the object is clearly visible; tracking runs both directions.
- **Edge Shrink (0–10 px)** — trims unreliable mask borders.
- **Three SAM2 model sizes** — Small (fast), Base+ (recommended), Large (best quality).
- **LichtFeld Studio and WebODM output modes.**

---

## Installation

**Windows installer:** download `SAM2_MaskTool_Setup.exe` from [Releases](../../releases). It installs Python 3.12 if needed, then the requirements.

**Manual:**

```
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu124
pip install sam2 huggingface_hub customtkinter pillow numpy opencv-python
```

Requires Python 3.10–3.12 and an NVIDIA GPU with CUDA (CPU works, but slowly).
The SAM2 model downloads automatically on first run.

---

## Usage

Launch **`SAM2 Mask Tool.vbs`** (or run `python mask_tool.py`).

### Keep an object

1. Output mode **LichtFeld** → browse to your `colmap_work` folder (must contain `images/`).
2. Task **Keep object**.
3. Left-click across the **whole** object (3–5 points), right-click the background (2–3 points).
4. Set **Edge Shrink** to 2 px → **Generate All Masks**.
5. LichtFeld Studio → **Mask Mode: Alpha Consistent**, **Alpha Mask: off** → train.

### Remove ghosts

1. Task **Remove ghosts**.
2. Left-click the person to remove. For another one: **+ New object**, then click them.
3. **Generate All Masks** — marked objects become black, everything else white.
4. LichtFeld Studio → **Mask Mode: Ignore**, **Alpha Mask: off** → train.

### Output

| Mode | File naming | Location |
|---|---|---|
| LichtFeld | `frame_0001.png` | `colmap_work/masks/` |
| WebODM | `frame_0001_mask.png` | next to the images |

White = reconstruct, black = excluded.

---

## Tips

- **Every frame you click must cover the whole object.** SAM2 defines the object on that frame only from that frame's clicks — clicking just a shoe makes the mask on that frame "a shoe".
- If tracking drifts (e.g. onto a shadow), jump to that frame and add clicks there.
- For people, a short capture with the subject standing still gives far sharper splats than a long one.

---

## License

MIT — see [LICENSE](LICENSE).

## Credits

- **[SAM2](https://github.com/facebookresearch/sam2)** — Segment Anything Model 2 by Meta AI (Apache 2.0)
- **[LichtFeld Studio](https://lichtfeld.io)** — 3D Gaussian Splatting trainer
- **[WebODM](https://github.com/OpenDroneMap/WebODM)** — open-source photogrammetry by OpenDroneMap
- Built by [OpsAbove](https://www.opsabove.com)

---

<p align="center">
  Built with ☕ by <a href="https://www.opsabove.com">OpsAbove</a> ·
  <a href="https://ko-fi.com/opsabove">Support on Ko-fi</a>
</p>
