# MARK

A single-page personal browser start page: a live clock, a customizable looping video wallpaper, an ambient lo-fi audio toggle, and a bookmark grid that ranks entries by visit count.

## What it does

- **Onboarding** — asks for your name on first visit and remembers it via `localStorage`.
- **Clock** — large live clock with a date line, updated every 30 seconds.
- **Bookmarks** — up to 9 bookmarks shown in a grid, auto-sorted by how often each one is clicked. Add, edit, and delete bookmarks through a modal.
- **Video wallpaper** — pick a full-screen looping background video from three preset categories: Anime, Landscape, Lo-Fi.
- **Lo-Fi audio** — optional ambient audio stream toggle in the top bar.
- **Settings panel** — a slide-in panel to:
  - change your display name
  - pick a clock font (8 presets) and size
  - choose a video wallpaper
  - toggle a "heavy graphics" blur mode
  - set the bookmark grid size (3–4 columns, 3–6 rows)
  - export/import all your data as a JSON file

## Running it

This is a static, single-file app with no build step or dependencies. Open `index.html` directly in a browser, or serve the folder with any static file server.

## Data & storage

All state (name, bookmarks, and preferences) is stored locally in the browser's `localStorage`. Nothing is sent to a server. Use **Settings → Data → Export** to back up your data as JSON, and **Import** to restore it.

## Project structure

- `index.html` — the entire application (markup, styles, and JavaScript in one file)
- `README.md` — this file

See `ARCHITECTURE.md` for implementation details.
