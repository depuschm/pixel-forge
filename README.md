# PIXELFORGE

**A pixel-perfect pipeline for converting Nano Banana Pro screenshots into clean pixel art.**

Single-file, zero-dependency, runs entirely in the browser.

🔗 **[Live Demo](https://depuschm.github.io/pixel-forge/)**

![PIXELFORGE screenshot](screenshot.png)

---

## Features

### Pipeline

The tool processes images through four configurable steps, each with a live preview:

**Step 1 — Sprite Detection** *(optional)*
Automatically detects sprites separated by fully transparent pixels. Click any detected sprite to process it individually instead of the full image.

**Step 2 — Pixel Size Detection**
Detects the upscale factor of the source image using an F-ratio block uniformity algorithm across averaged row/column luminance profiles. Supports pixel sizes from 1 (native resolution) upward. Includes a zoomable, pannable grid overlay preview so you can verify alignment. Manual override available.

**Step 3 — Noise Removal & Palette** *(optional)*
- **Noise removal** — merges similar-colored pixels by color distance threshold
- **Palette consolidation** — quantizes to a target color count using k-means, Wu's, Median Cut or Octree, optionally in OKLab space
- **Custom palette** — upload your own palette and remap all colors to it exactly
- **Color matching** — choose how pixels are matched to the palette:
  - OKLab nearest (default)
  - CIE LAB nearest
  - RGB nearest
  - Floyd-Steinberg error diffusion dithering
  - Bayer ordered dithering (2×2, 4×4, 8×8)
  - Dithering strength slider to blend between dithered and nearest-color

**Step 4 — Scale Down**
Reduces the image to true pixel art resolution by sampling the dominant color of each pixel block (mode sampling).

---

### Palette Formats

Custom palettes can be loaded from:

| Format | Extension | Notes |
|--------|-----------|-------|
| PNG | `.png` | Samples all unique opaque colors |
| GIMP Palette | `.gpl` | Plain-text `R G B Name` format |
| JASC PAL | `.pal` | Paint Shop Pro / Lospec |
| HEX | `.hex` | One `#RRGGBB` per line, Lospec default |
| Adobe Color Table | `.act` | 768-byte binary, 256-color max |
| Adobe Swatch Exchange | `.ase` | RGB, CMYK, Gray color models |

---

### Other Features

- **Simple / Advanced mode** — advanced mode exposes extra controls (quantization algorithm, OKLab quantization, perceptual matching, neighbored noise removal, step reordering). Mode is persisted in `localStorage`.
- **Step reordering** — drag steps into any order in advanced mode
- **Drag-to-pan + zoom** on the pixel size preview for fine-grained grid inspection
- **Download** the final result as PNG

---

## Usage

No build step, no dependencies, no server required.

```
open index.html
```

Or serve it with any static file server:

```bash
npx serve .
# or
python -m http.server
```

Drop an image into the drop zone, the pipeline runs automatically. Adjust any step and it re-runs from that step onward.

---

## Technical Notes

**Pixel size detection** uses an F-ratio scorer that measures between-block contrast vs within-block variance for each candidate pixel size. Scores are normalized by `√P` to prevent large block sizes from winning via trivially low within-variance. Offset is found in a separate dedicated pass that minimises within-block variance independently per axis.

**Dithering** — Floyd-Steinberg uses a float error buffer with the standard `7/16 3/16 5/16 1/16` distribution. Bayer uses precomputed 2×2, 4×4, 8×8 threshold matrices with a ±16 per-channel offset. Both use OKLab for nearest-color matching internally.

**Color spaces** — OKLab conversion follows the [Björn Ottosson spec](https://bottosson.github.io/posts/oklab/). LAB uses the standard D65 illuminant.

---

## License

MIT
