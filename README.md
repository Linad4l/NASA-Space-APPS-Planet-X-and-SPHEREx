# Starfire's Flipbook

A free web tool (a "blink" tool for NASA SPHEREx data) that lets anyone flip through NASA SPHEREx infrared images of a patch of sky, like a short video, to spot objects that **move** or **change brightness**. Built for the NASA Space Apps Challenge 2026.

**Try it:** https://linad4l.github.io/NASA-Space-APPS-Planet-X-and-SPHEREx/

## What it does

1. You enter sky coordinates (RA and Dec) or pick one of the built-in targets.
2. The tool searches NASA's IRSA archive for every SPHEREx exposure that covers that spot.
3. It downloads small cutouts, lines them up on the same patch of sky, and plays them as a flipbook.
4. A caption under the flipbook says what it shows (object name or coordinates, detector, field of view, mode).

The sky stays fixed in the frame. Stars stay put, and anything that moves drifts across the picture. Anything that changes brightness flickers.

## Features

- RA/Dec input (degrees or sexagesimal) and a built-in list of the five required targets
- Live SPHEREx cutouts from NASA IRSA across all available dates, with a saved-example fallback if the archive is busy
- Play/pause, back/next, frame slider, speed, brightness, contrast, invert
- Field of view presets and a custom field of view
- Detector choice (D1 to D6, 0.75 to 5.0 microns)
- Display modes: every exposure, one stacked image per epoch, difference from the first epoch, difference from the previous epoch
- Optional skip of exposures that are mostly empty
- Brightness-at-center chart over time
- Optional proper motion: shifts the center of the picture to where a fast-moving object is during the SPHEREx observations (filled in automatically for the built-in targets)
- Data-quality info: percent of the field with no data and percent of flagged pixels
- Link to open the same spot in Aladin Lite
- Shareable links (the settings are stored in the URL) and an embed mode (`?embed=1`)

## Test targets

Barnard's Star, Epsilon Indi (A + B), WISE 0855, RW Aurigae, R Draconis. Each has a button in the tool.

## How it works

- Everything runs in the browser (plain HTML and JavaScript, no build step).
- The browser cannot call IRSA directly because of CORS, so a tiny Cloudflare Worker (`worker.js`) relays requests. It only forwards to a short allowlist of astronomy hosts.
- The tool queries IRSA's SIA service for the `spherex_dr1` and `spherex_qr3` collections, downloads FITS cutouts, parses them in the browser, and resamples them onto a common north-up grid.
- Each frame gets a median background subtraction and an asinh stretch.
- Pre-made example frames (`data/`) were generated in a Google Colab notebook and are used if the live archive fails.

## Data

SPHEREx images come from the NASA/IPAC Infrared Science Archive (IRSA). SPHEREx is a NASA mission operated by JPL and Caltech. Target coordinates and proper motions were checked in SIMBAD.

## Known limits

- SPHEREx exposures are small and have 6.15 arcsecond pixels, and the mission has only been observing since 2025. Slow movers shift by only a pixel or two, so motion is subtle compared with long-baseline tools like WiseView.
- Each exposure covers a limited area, so a target near an exposure edge shows dark blue "no data" areas. Mostly empty exposures are skipped by default.
- The very bright cores of stars can be missing (detector saturation).
- Frames are aligned using the basic image coordinates only. Small distortion terms are ignored, so stars near an exposure edge can be off by a few pixels.
- Name lookup works for the built-in targets. Other names may fail, so enter coordinates instead.
- Not built yet: Gaia and WISE overlays, spectra, per-detector co-adds, explanation of individual quality-flag bits.
- The NASA archive is sometimes slow or busy. The tool retries, and if it still fails, try again in a minute.

## Run it yourself

Open `index.html` in a browser, or host the repo on GitHub Pages. To use your own relay, deploy `worker.js` to Cloudflare Workers and set `DEFAULT_PROXY` in `index.html` (or pass `?proxy=https://your-worker`).

## Team

NASA Space Apps Challenge 2026, San Jose State University team. (Add team names here.)
