# Starfire's Flipbook

A free web tool (a "blink" tool for NASA SPHEREx data) that lets anyone flip through NASA SPHEREx infrared images of a patch of sky, like a flipbook or short video, to spot objects that **move** or **change brightness**. Built for the NASA Space Apps Challenge 2026.

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

How to get the best results

Quick start: Press one of the target buttons (for example Barnard's Star), then press Make flipbook. A run takes about one to two minutes because the images come live from NASA.

Recommended settings

Setting	Best choice	Why
Field of view	0.1° (6 arcminutes)	Wide enough to see neighbors, small enough to see detail. Use 0.4° for a wide system like Epsilon Indi, or a small custom value (0.03° to 0.05°) to zoom in on a mover.
Detector	D6 (4.4 to 5.0 microns) for cool brown dwarfs. D2 (1.1 to 1.6 microns) for ordinary stars and variable stars	Cool objects glow brightest at longer wavelengths. Ordinary stars are brighter at shorter ones.
What to show	One stacked image per epoch to spot movers, or Epoch minus first epoch to see only what changed	Stacking averages each visit into a cleaner picture, and subtraction cancels out the unchanging stars.
Max exposures	24 (default)	A good balance of coverage and speed. Raise it to 48 or 96 for more dates.
Skip partly-empty exposures	Yes (default)	Hides frames where your target is near an exposure edge.
Speed	Slower for movers, faster for variables	Slow playback makes small shifts easier to follow.

What to look for

Moving objects (asteroids, nearby stars, brown dwarfs): Watch one dot against the fixed background stars. It slides slowly across the frame from one visit to the next.
Variable objects (variable stars, supernovae, quasars): Watch for a dot that gets brighter or dimmer without moving. The chart under the flipbook shows brightness at the center over time.
Epoch minus first epoch: A mover appears as a bright spot beside a dark spot. A brightening or dimming object appears as a single bright or dark blob.

Tips

Pick a smaller field of view to make slow motion bigger on screen.
If the NASA archive is busy and a run fails, wait a minute and press Make flipbook again.
Use Copy link to this view to share the exact settings with someone.
The tool includes saved examples, so you can still see a flipbook when the archive is slow.

## Run it yourself

Open `index.html` in a browser, or host the repo on GitHub Pages. To use your own relay, deploy `worker.js` to Cloudflare Workers and set `DEFAULT_PROXY` in `index.html` (or pass `?proxy=https://your-worker`).

## Team

NASA Space Apps Challenge 2026, San Jose State University team "StarFire" - Lina Bourouha, Laila Ben Ameur, Sarah Ayadi, Mariam Jamil
