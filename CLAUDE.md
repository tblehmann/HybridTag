# CLAUDE.md

Guidance for working in the **TagAPP** project.

## What this is

TagAPP is a **mobile-first HTML database** cataloging **wood / mass-timber (CLT)
buildings** in California. It is meant to be **hosted on GitHub Pages** and reached by
**NFC tap-to-scan from a phone** — every physical board carries a serial number that
deep-links to that building's record. Design for a small touchscreen first.

Each building has a **record page** (map location + spec data). A scanned tag resolves
to one building by its **board serial number** (e.g. `TAG-CA-2B9C74`).

## Current state (single reference page)

`index.html` is **one hand-built record page** used to lock the layout and aesthetic —
the Santa Cruz Boardwalk Coaster "The Dipper", serial `TAG-CA-2B9C74`. It is **not yet
templated**; the 50 records in `boards/boards.md` are not wired up. Keep iterating on
this single page until the design is approved, then template it across all records.

## Aesthetic — industrial datasheet

The visual target is a **technical spec-sheet / engineering datasheet**. Reference image:
`Images/textLayout.png` (an "ARC / P1 Industrial Robot" product sheet). Key ingredients,
all implemented in `index.html`:

- **Navy left rail** (`#3C496B`) running full height, with a large rotated
  "BUILDING RECORD" label and a `↗` arrow at the bottom.
- **Hard hairline grid** — every section is a bordered cell (1.5px navy rules), giving
  the drafting-sheet frame.
- **Big Heavy display type** — building name, serial header, address in Cooper Hewitt
  Heavy; tight leading, negative tracking.
- **ALL-CAPS letterspaced labels** in Cooper Hewitt Bold ("SERIAL NO.", "LOCATION",
  spec keys).
- **Serial number** shown big as a header near the top (it's the scanned tag's identity).
- **Spec table** with a solid navy header row; left key labels are larger than the
  right-hand values.
- **Map as "Fig. 01"** — a framed Leaflet map standing in for the reference's technical
  line-drawing, with a caption bar and a Lat/Lng readout.

Section order top→bottom: meta strip → serial header → address → building name + blurb →
map figure → spec table → footer.

### Design guardrails (from user feedback — respect these)
- **No yellow.** The `#EA9B21` highlight was pulled; the `--amber` token is repointed to
  navy. Keep the palette two-tone (navy on gray) unless told otherwise.
- **No decorative sparkles / star glyphs** — a 4-point star read as the Gemini logo.
- **Type must be thick** — use Heavy for display, Bold for labels. Semibold was too light.
- Keep it restrained; avoid gimmicky "feature boxes."

## Palette

- **Main / paper:** `#EDEDED`  (page background; page gutter is `#d9d9d9`)
- **Secondary / navy:** `#3C496B`  (rail, table header, all hairline rules, ink accents)
- **Text ink:** `#2b3350`
- **Highlight:** `#EA9B21` — **currently unused** (removed per feedback; token kept for
  future).

## Typography — Cooper Hewitt only

Andada was tried and removed. The whole UI is Cooper Hewitt.

- **Display** (`--display`) → Cooper Hewitt **Heavy** — big headers.
- **Titles / labels** (`--title`) → Cooper Hewitt **Bold** — loaded under the family name
  `"Cooper Hewitt"`; every uppercase label/spec key uses it.
- **Body** (`--body`) → Cooper Hewitt **Book** — paragraphs and spec values.

Web fonts are **self-hosted** as `woff2` (with `woff` fallback) in **`fonts/`**. They were
converted from the source OTFs with fonttools (`ft.flavor = "woff2"`). Old woff1 files
caused silent fallback to a system font — always ship **woff2**.

## Fonts on disk

- **`Font/`** — authoritative **source OTFs** (all Cooper Hewitt weights Thin→Heavy +
  italics, AndadaPro variable, and `TRIAL-Vanity-Condensed.otf`). Convert new weights
  from here.
- **`fonts/`** — web-ready `woff2`/`woff` actually referenced by `index.html`
  (Cooper Hewitt Book / Bold / Heavy / Semibold).

To add a weight: `fonttools` → load the OTF from `Font/`, set `ft.flavor="woff2"`, save
into `fonts/`, add an `@font-face`.

## Tech stack

- **Vanilla HTML/CSS/JS**, single file, no build step, no backend — static, GitHub-Pages
  ready.
- **Map:** Leaflet `1.9.4` from unpkg (with SRI `integrity` hashes) + **CartoDB Positron**
  (`light_all`) tiles — free, no API key, muted look that suits the palette. Custom navy
  `divIcon` pin.
- CSS lives in a single `<style>` block; design tokens in `:root`.

## Data

`boards/boards.md` — **50 fake seed records**, California wood buildings. Format is
`key: value` lines, records separated by `---`, for easy parsing. Fields: `serial`,
`name`, `type`, `address`, `city`, `state`, `lat`, `lng`, `construction_type`,
`construction_date`, `demolition_date`. Date rules: demolition ≥20 yrs after
construction, not in the future, none within the last 6 months. This list is meant to
**grow** — keep the schema extensible.

## Running / mobile preview

Static site — serve the folder and open in a browser:

```
python3 -m http.server 8000 --bind 127.0.0.1   # localhost only
```

Then http://localhost:8000. **Mobile preview:** DevTools → device toolbar (⌘⇧M in
Chrome), pick iPhone/Pixel. To view on a real phone you must bind `0.0.0.0` (blocked by
default for safety — run it yourself via `! python3 -m http.server 8000 --bind 0.0.0.0`
and hit the Mac's LAN IP).

## Open / next steps

- **Template** the approved `index.html` across all 50 records in `boards/boards.md`
  (generate one page per board, or one page + JS that reads the parsed data).
- Decide the **NFC → record** mechanism (URL param on the serial, e.g. `?tag=TAG-CA-...`).
- A future **index/map view** listing all buildings (the original brief's map).

## Key files

- `index.html` — the single reference record page (design in progress).
- `boards/boards.md` — 50 seed records.
- `Project Brief.md` — original brief. `Style Guide.md` — still empty (this file is the
  living style reference for now).
- `Images/textLayout.png` — the datasheet aesthetic reference. `Images/CLT.jpg`,
  `constructionSiteCLT.png` — mass-timber context imagery.
