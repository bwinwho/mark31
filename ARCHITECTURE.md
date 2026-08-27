# Architecture

## Overview

MARK is a single static HTML file (`index.html`) with inline CSS and inline JavaScript. There is no build tool, framework, bundler, or backend — the file can be opened directly in a browser or served as-is.

## Layers

### Markup & styles
All CSS lives in a `<style>` block in the document head. The visual system is a dark, full-bleed video background with a gradient veil overlay, translucent "glass" surfaces, pill-shaped controls, and a mix of serif/display and monospace web fonts (loaded from Google Fonts).

### Application state
State is held in plain JavaScript variables at the top of the `<script>` block:

- `userName` — the display name entered during onboarding
- `bookmarks` — array of `{id, name, url, visits, created}` objects
- `pFont`, `pSize`, `pWallId`, `pWallSrc` — pending/selected settings values used by the settings panel before they're applied

### Persistence
All persistence goes through `localStorage`, read/written directly (no wrapper/store abstraction):

| Key | Purpose |
|---|---|
| `mark_name` | user's display name |
| `mark_bookmarks` | JSON array of bookmarks |
| `mark_blur` | heavy-graphics (blur) toggle state |
| `mark_grid_cols` / `mark_grid_rows` | bookmark grid dimensions |
| `mark_clock_font` / `mark_clock_size` | clock style |
| `mark_wall_id` / `mark_wall_src` | selected video wallpaper |

### Rendering
No framework or virtual DOM is used. UI updates are done via direct DOM manipulation (`innerHTML`, `classList`, `style` property writes) triggered by explicit function calls (e.g. `renderBookmarks()`, `buildFontGrid()`, `buildWallGrids()`).

### Views
The app has two main visual layers, toggled by class/transform changes rather than routing:
- `#app` — the main clock + bookmark grid view
- `#settings-panel` — a full-height panel that slides in from the right

A modal overlay (`#bm-modal`) handles adding/editing a single bookmark.

## Data flow

1. `init()` runs on load: reads all `localStorage` keys, applies saved preferences (grid size, blur, clock font/size, wallpaper), and shows either the onboarding screen or the main app.
2. User actions (adding a bookmark, changing a setting, clicking a wallpaper thumbnail) mutate in-memory state, persist it to `localStorage`, and re-render the affected DOM section.
3. Bookmarks are re-sorted by `visits` (descending) each time `renderBookmarks()` runs, so frequently used links float to the front of the grid.
4. **Export** serializes `{userName, bookmarks, exportedAt}` to a downloaded JSON file. **Import** reads a JSON file and overwrites the corresponding `localStorage` values, then re-renders.

## External resources

The app loads assets from external URLs at runtime (not bundled):
- **Google Fonts** — clock/UI typefaces
- **Google favicon service** (`google.com/s2/favicons`) — per-bookmark favicons
- **Video CDN URLs** — the preset wallpaper videos (Anime/Landscape/Lo-Fi categories)
- **Lo-fi audio stream** — background audio for the Lo-Fi toggle

There is no server-side component; all logic and state management run client-side in the browser.
