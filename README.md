# PixelVault Dashboard

A Flask dashboard for the PixelVault image-security suite. It shows all 5 planned stages of the suite as a pipeline, and lets you run each one that's built.

**Built so far:** Module 1 (Visual Cryptography), Module 2 (Robust Watermarking). Modules 3–5 are shown as "coming soon."

## Quick start

```bash
pip install -r requirements.txt
python app.py
```

Open **http://localhost:5000** in your browser.

## Module 1 — Visual Cryptography

- **Split** — upload any image and get back 5 noise-like shares in a `.zip`. All 5 shares are required to reconstruct the original.
- **Combine** — upload all 5 shares to get the reconstructed image back, pixel-perfect by default. A "Photo mode" checkbox is available for photos and gradients (it halftones instead of using a plain threshold); it's off by default so text, diagrams, and screenshots stay crisp.

## Module 2 — Robust Watermarking

- **Embed** — upload an image plus a short identifier (username, ID, timestamp — up to ~14 characters). You get back the watermarked image and a **key file** (`.npz`). This is a non-blind scheme, so keep the key file — it's required to ever extract the mark again.
- **Extract** — upload a watermarked image plus its key file to get the identifier back, along with a confidence score.
- **Test robustness** — embeds a test identifier and runs it through JPEG compression, resizing, noise, blur, and cropping, showing which attacks the mark survives. The mark is written into two sub-bands internally (one tuned for JPEG/noise, one for resizing); extraction automatically uses whichever one comes back cleaner. In practice it reliably survives JPEG compression (quality 30–95) and moderate noise; heavy resizing, blurring, or cropping can still break it — those need geometric synchronization work beyond this build's scope.
- **Try an attack yourself** — pick a specific attack and strength, apply it to a watermarked image, and get back the attacked image plus the extraction result in a `.zip`, so you can inspect any case directly. The input image doesn't need to be a JPEG — any format works; the tool creates the degraded version internally.

## Project layout

```
app.py                Flask routes for the dashboard + both modules
visual_crypto.py      Module 1: (n,n) visual-crypto encode/decode logic
watermark.py          Module 2: DWT-DCT-SVD watermark embed/extract
templates/
  base.html            Shared layout
  index.html           Dashboard (pipeline of all 5 modules)
  module1.html          Visual crypto split/combine tool page
  module2.html          Watermark embed/extract/robustness-test tool page
  coming_soon.html      Placeholder for modules 3–5
static/style.css        Styling
uploads/, tmp/           Scratch folders used during processing (auto-cleaned)
```

## Adding modules 3–5

Each future module gets its own `moduleN.html` and routes in `app.py`, and its `MODULES` entry in `app.py` flipped to `"status": "ready"` — the dashboard link activates automatically.

## Before deploying

`app.py` uses a hardcoded dev secret key for flash messages. Replace it with an environment-variable-based secret before deploying this anywhere beyond your own machine.

## License

Add a license of your choice (e.g. MIT) before making this repository public.
