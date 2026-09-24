# MARK — Agent Guide

Project: MARK
Platform: Static web (client-side browser page)
Document Role: Technical operating manual for AI coding agents
Last Verified: 2026-09-24
Verified Against: git commit edf74a2 (branch claude/optimistic-goldberg-vucex5)
Current Version Name: Not applicable (no version field exists in this project)
Current Version Code/Build: Not applicable
Status: Active

## 1. Project Snapshot

MARK is a personal browser start page ("new tab" style page): a live clock, a
looping video wallpaper, an ambient lo-fi audio toggle, and a visit-ranked
bookmark grid, all configurable through a slide-in settings panel.

- **Language:** HTML, CSS, vanilla JavaScript (ES6+), no TypeScript.
- **Framework:** None. No React/Vue/Compose/etc.
- **Build system:** None. No bundler, no package manager, no `package.json`.
- **Entry point / whole application:** `index.html` (single file, ~786 lines —
  markup, all CSS in one `<style>` block, all JS in one `<script>` block).
- **Backend:** None. Purely client-side; the only "server" interactions are
  fetches of static third-party assets (fonts, favicons, videos, an audio
  stream) issued directly by the browser.
- **Persistence:** Browser `localStorage` only, read and written directly
  (no state-management library, no wrapper/store abstraction).
- **Supporting docs in repo root:** `README.md` (user-facing overview),
  `ARCHITECTURE.md` (existing technical overview — still accurate, largely
  superseded in detail by this file).

## 2. Agent Golden Rules

- This is a ~40KB single file. Read all of `index.html` before editing any
  part of it — there is no module boundary to hide behind, so an inline
  `<script>` change three sections away can break something distant.
  "Inspect before modifying" applies literally here.
- Prefer the smallest viable diff. This file has already been simplified once
  (see `CodeDoc/history.md` — greeting and task-list features were
  deliberately removed); do not reintroduce complexity without being asked.
  Current source is the request; a feature not present in `index.html` today
  is not present in the product, whatever `ARCHITECTURE.md`'s early revisions
  might have implied historically.
- Do not create a build step, bundler, framework dependency, or package.json
  unless the user explicitly asks for one. The zero-build nature is a
  deliberate project property, not an oversight.
- Do not introduce a new state-management abstraction (store, reducer,
  observable) for what is currently plain global variables + direct
  `localStorage` calls. Keep the existing pattern consistent.
- `localStorage` keys (see §9) are the single source of truth for all
  persisted state. Never invent a second storage mechanism (e.g.
  `sessionStorage`, cookies, IndexedDB) for the same data.
- Never hardcode credentials or private API keys into `index.html` — it is a
  fully public, client-visible file. (Current external URLs used are public,
  unauthenticated asset/stream URLs — see §7.)
- Run the page in a browser (or static file server) after any change; there
  are no automated tests to catch regressions (see §13).

## 3. Architecture at a Glance

```
Browser loads index.html
  → init() runs on DOMContentLoaded-equivalent (called at end of <script>)
      → reads all localStorage keys
      → applies saved preferences (grid size, blur, clock font/size, wallpaper)
      → shows #onboard (if no saved name) OR shows #app and calls boot()
      → boot() → renderBookmarks()
      → startClock() begins a 30s setInterval loop
      → buildSettingsUI() pre-builds the settings panel's font/wallpaper grids
  → user interaction (add bookmark, change setting, pick wallpaper, etc.)
      → mutates an in-memory JS variable
      → immediately persists the relevant key(s) to localStorage
      → re-renders only the affected DOM section (no diffing, direct DOM writes)
```

There is no routing and no page navigation. "Views" (`#app` main view vs.
`#settings-panel`) are toggled by CSS class/transform, not by URL or route
state.

## 4. Project / File Map

Everything lives in one file. Use this table to jump to a section within
`index.html` by role (line numbers approximate, verify against current file):

| Area | Location in `index.html` | Responsibility | Risk/Notes |
|---|---|---|---|
| Global CSS variables & reset | lines 9–19 | Color tokens (`--text`, `--muted`, `--bd`, etc.) | Low risk to tweak values |
| Video background + veil | lines 23–25, 204–205 | Full-bleed looping wallpaper video + gradient overlay | `#bgv` src is set only via JS (`setVideo()`), never a static `src` attr |
| Onboarding overlay | lines 28–29, 207–216 | First-run name capture | Gates whether `#app` or `#onboard` shows on boot |
| Settings panel CSS/markup | lines 100–200 (CSS), 241–357 (HTML) | Slide-in panel: name, clock style, wallpaper, blur, grid size, data import/export | Largest single feature area |
| Bookmark grid CSS/markup | lines 65–82 (CSS), 234–236 (HTML `#bm-grid`) | Visit-ranked bookmark tiles | `MAX_BM` constant caps count at 9 |
| Bookmark modal | lines 84–99 (CSS), 360–371 (HTML) | Add/edit a single bookmark | Reused for both add and edit via hidden `#bm-eid` field |
| Toast | lines 184–186 (CSS), 373 (HTML) | Transient confirmation messages | `toast()` JS helper |
| Data constants (`FONTS`, `WGRADS`, `WALLS`) | JS lines ~379–429 | Font presets, wallpaper thumbnail gradients, wallpaper video catalog | Editing `WALLS` changes available wallpapers; keep `WGRADS` keys in sync with `WALLS` ids |
| State variables | JS lines ~435–442 | `userName`, `bookmarks`, `pFont`, `pSize`, `pWallId`, `pWallSrc` | See §5 for ownership |
| Boot sequence | `init()`, `boot()`, `setVideo()`, `applyClockStyle()` — JS lines ~447–493 | App startup | Do not reorder without checking onboarding gate logic |
| Clock | `startClock()` — JS lines ~510–524 | Live clock + dateline, ticks every 30s | Uses 12-hour format with AM/PM, not configurable |
| Bookmarks logic | `renderBookmarks()`, `trackVisit()`, `saveBM()`, `openBmModal()`, `saveBookmark()`, `deleteBm()` — JS lines ~529–595 | CRUD + visit tracking + sort-by-visits render | `trackVisit()` re-renders after a 300ms delay so the click completes first |
| Settings logic | `openSettings()`, `closeSettings()`, `saveName()`, `buildFontGrid()`, `applyClock()`, `resetClock()`, `buildWallGrids()`, `applyWall()`, `switchWCat()` — JS lines ~600–718 | All settings-panel interactions | "Pending" values (`pFont`/`pSize`/`pWallId`/`pWallSrc`) are only committed to `localStorage` on explicit Apply/Save clicks, except wallpaper preview which live-updates `#bgv` on click before Save |
| Data export/import | `exportData()`, `importData()` — JS lines ~719–738 | JSON backup/restore | Import overwrites `bookmarks` and `userName` only; does not restore style/wallpaper/grid settings |
| Graphics/blur toggle | `toggleBlur()` — JS lines ~740–746 | Adds/removes `.heavy-graphics` body class (backdrop-filter blur) | Purely cosmetic; no functional dependency elsewhere |
| Grid size control | `setGrid()` — JS lines ~748–761 | Sets `--bm-cols`/`--bm-rows` CSS custom properties | Grid can be sized larger (up to 4×6=24 slots) than `MAX_BM` (9) — see §17 |
| Lo-fi audio | `toggleLofi()` — JS lines ~763–767 | Play/pause the `#lofi` `<audio>` element | Stream URL is a public third-party lo-fi radio stream, not project-owned |
| Utilities | `uid()`, `esc()`, `toast()` — JS lines ~769–777 | ID generation, HTML-escaping for bookmark labels, toast display | `esc()` is the only injection guard — see §11 |

## 5. System Ownership / Sources of Truth

| Concern | Owner | Main Consumers | Do Not Duplicate In |
|---|---|---|---|
| Display name | `userName` (JS var) + `localStorage['mark_name']` | Onboarding gate, settings name field | Do not read name from anywhere but this key |
| Bookmarks | `bookmarks` (JS array) + `localStorage['mark_bookmarks']` | `renderBookmarks()`, export/import | Do not create a second bookmark list |
| Clock style (font/size) | `pFont`/`pSize` (pending) + `localStorage['mark_clock_font']`/`['mark_clock_size']` | `applyClockStyle()`, settings preview | Applied clock style always comes from these two keys, not from `pFont`/`pSize` directly (those are only "pending" until Apply is clicked) |
| Wallpaper selection | `pWallId`/`pWallSrc` (pending) + `localStorage['mark_wall_id']`/`['mark_wall_src']` | `setVideo()`, settings wallpaper grid | Note: clicking a wallpaper thumbnail updates the live video immediately via `setVideo()`, but only *persists* on `applyWall()` |
| Bookmark grid dimensions | `localStorage['mark_grid_cols']`/`['mark_grid_rows']` | CSS custom properties `--bm-cols`/`--bm-rows` | Read directly from `localStorage` in `setGrid()`, not cached in a JS variable |
| Blur/heavy-graphics toggle | `localStorage['mark_blur']` (`'1'`/`'0'` string) | `body.heavy-graphics` class | — |

## 6. Feature Architecture

### Onboarding
- Purpose: capture the user's display name on first visit.
- Files: `#onboard` markup (index.html:207–216), `saveUser()` (JS).
- Source of truth: presence/absence of `localStorage['mark_name']`.
- Flow: `init()` checks `userName`; if empty, `#onboard` stays visible and
  `#app`/`boot()` never runs until `saveUser()` fires.
- Edge case: an empty/whitespace-only name is rejected (`if (!n) return;`) —
  the user is stuck on onboarding until they enter something.

### Bookmarks
- Purpose: a personal, most-used-first link launcher, capped at 9 entries.
- Files: `renderBookmarks()`, `saveBookmark()`, `trackVisit()` (JS).
- Source of truth: `bookmarks` array, persisted verbatim to
  `localStorage['mark_bookmarks']` as JSON.
- Sort: descending by `visits` count, recomputed on every render — the array
  order in storage is not the display order.
- Fallback: favicons come from `google.com/s2/favicons`; a broken/blocked
  request hides the `<img>` via `onerror` rather than showing a broken-image icon.

### Video wallpaper
- Purpose: full-screen looping ambient background, chosen from 3 preset
  categories (Anime, Landscape, Lo-Fi) of 6 videos each (`WALLS` constant).
- Files: `setVideo()`, `buildWallGrids()`, `applyWall()` (JS).
- Source of truth: `localStorage['mark_wall_id']` / `['mark_wall_src']`;
  default is `WALLS.anime[0].src` (`DEFAULT_WALL`).
- All 18 videos are hosted on two external CDNs (Supabase Storage,
  CloudFront) — see §7. There is no local/bundled fallback video.

### Settings panel
- Purpose: single surface for all user-configurable preferences.
- Files: `#settings-panel` (index.html:241–357), all `s-*`-prefixed JS
  functions.
- Pattern: most controls use a "pending" value that only writes to
  `localStorage` when an explicit Apply/Save button is clicked (clock style,
  wallpaper); a few write immediately on interaction (name save, blur toggle,
  grid size — these have no separate Apply step).

### Data export/import
- Purpose: manual backup/restore since there is no server sync.
- Files: `exportData()`, `importData()` (JS).
- Behavior: exports `{userName, bookmarks, exportedAt}` as a downloaded
  `mark-backup.json`. Import only restores `bookmarks` and `userName` — it
  does **not** restore clock style, wallpaper choice, blur, or grid size.
  This is current behavior, not a bug to silently "fix" without confirming
  with the user first.

## 7. Data Sources & External Systems

| System | Technology | Supplies | Persistence/Fallback | Notes |
|---|---|---|---|---|
| Google Fonts | `<link>` + `@import`-style stylesheet | 8 typefaces used for clock/UI | None — if blocked, browser falls back to `sans-serif`/`serif`/`monospace` generic families already specified in each `font-family` stack | No self-hosted font fallback |
| Google favicon service (`google.com/s2/favicons`) | Public image endpoint | Per-bookmark favicon images | `onerror` hides the broken image | No caching of favicons locally |
| Supabase Storage (`jvaghslcaurgedzzcwah.supabase.co`) | Public object storage URLs | Anime + some Lo-Fi category wallpaper videos | None — broken URL leaves a blank/black video area, gradient (`WGRADS`) shows in the settings thumbnail regardless | Not project-owned infrastructure; URLs are hardcoded in `WALLS` |
| CloudFront CDN (`d8j0ntlcm91z4.cloudfront.net`) | Public video CDN URLs | Landscape + some Lo-Fi category wallpaper videos | Same as above | Same caveat |
| Lo-fi audio stream (`lofi.stream.laut.fm`) | Public internet radio stream | Background audio for the Lo-Fi toggle | Browser's native `<audio>` error handling; no in-app fallback stream | Playback requires user gesture per browser autoplay policy — this is why it's a manual toggle, not autoplay |
| `localStorage` | Browser Web Storage API | All persisted app state | See §9 | The only persistence layer in the project |

No REST/GraphQL API, no Firebase/Supabase database (only Supabase *storage*
buckets for static video files), no authentication system.

## 8. Settings / Configuration Contract

| Setting | Symbol/Key | Default | Effective Behavior | Persistence |
|---|---|---|---|---|
| Display name | `userName` / `mark_name` | `''` (empty → onboarding shown) | Shown nowhere in the UI currently except as stored data (the "greeting" feature that displayed it was removed — see history.md) | `localStorage` |
| Clock font | `pFont` / `mark_clock_font` | `'cinzel'` | Sets `#clock`'s `font-family` on Apply | `localStorage` |
| Clock size | `pSize` / `mark_clock_size` | `96` (px) | Sets `#clock`'s `font-size` on Apply; slider range is 48–240px | `localStorage` |
| Wallpaper | `pWallId`/`pWallSrc` / `mark_wall_id`/`mark_wall_src` | `WALLS.anime[0]` ("Evil Deity") | Sets `#bgv` video source; preview plays live on thumbnail click, persists on Apply | `localStorage` |
| Heavy graphics (blur) | — / `mark_blur` | `'0'` (off) | Toggles `body.heavy-graphics`, which adds `backdrop-filter: blur(18px)` to bookmark buttons and action buttons | `localStorage`, applied immediately (no separate Apply step) |
| Bookmark grid columns | — / `mark_grid_cols` | `'3'` | Sets CSS var `--bm-cols`; choices are 3 or 4 | `localStorage`, applied immediately |
| Bookmark grid rows | — / `mark_grid_rows` | `'3'` | Sets CSS var `--bm-rows`; choices are 3, 4, 5, or 6 | `localStorage`, applied immediately |

There is no server-side or environment-level override of any setting — the
stored value and the effective value are the same thing once Apply/toggle
fires.

## 9. State / Persistence / Cache Model

All persisted state lives in `localStorage`, scoped to the browser origin
serving `index.html`. Keys:

| Key | Contents |
|---|---|
| `mark_name` | display name string |
| `mark_bookmarks` | JSON array of `{id, name, url, visits, created}` |
| `mark_blur` | `'1'` or `'0'` |
| `mark_grid_cols` | `'3'` or `'4'` |
| `mark_grid_rows` | `'3'`\|`'4'`\|`'5'`\|`'6'` |
| `mark_clock_font` | one of the `FONTS` ids (e.g. `'cinzel'`) |
| `mark_clock_size` | numeric string, px |
| `mark_wall_id` | one of the `WALLS.*[].id` values |
| `mark_wall_src` | full video URL matching `mark_wall_id` |

- **Survives reload/restart:** yes — `localStorage` persists across browser
  restarts on the same origin.
- **Survives logout:** not applicable — there is no login/auth system.
- **Survives reinstall/clearing browser data:** no — clearing site data or
  `localStorage` for the origin wipes everything; this is why Export/Import
  exists.
- **Invalidation:** none automatic. Nothing expires or is versioned; a stale
  or malformed value (e.g. hand-edited `localStorage`) is trusted as-is
  except for `importData()`'s `try/catch` around `JSON.parse`.

## 10. Critical Runtime Architecture

No complex runtime system (no media pipeline beyond a plain `<video>`/`<audio>`
tag, no sync engine, no native integration, no background workers). This
section is intentionally omitted beyond noting: the video wallpaper element
(`#bgv`) has its `src` set purely via JavaScript (`setVideo()`); never add a
static `<source>` child or a hardcoded `src` attribute in markup, since that
would fight with `setVideo()`'s runtime assignment.

## 11. Reliability / Fallback Contracts

- Favicon load failure → `<img onerror>` hides the icon; label text remains.
- Wallpaper video load failure → no explicit fallback; the gradient
  (`WGRADS`) is only used for settings-panel thumbnails, not as an in-app
  background fallback.
- Import of malformed JSON → caught by `try/catch` in `importData()`, shows
  a `"Invalid file"` toast; no partial state is written.
- Bookmark name rendering uses `esc()` to HTML-escape user-entered names
  before inserting via `innerHTML` — this is the only injection guard in the
  codebase. Bookmark URLs are inserted into an `href` attribute unescaped
  (values come from the user's own bookmark list, entered by the same
  browser user, not from an external/untrusted source).
- No network-retry logic anywhere; all external asset loads are
  fire-and-forget browser requests.

## 12. Build Variants / Environments

None. There is exactly one environment: the static `index.html` file served
or opened as-is. No dev/staging/prod split, no environment variables, no
feature flags.

## 13. Build / Test Commands

There is no build step and no automated test suite.

```bash
# Run locally — either works, no install step required:
open index.html                     # macOS, opens directly in default browser
python3 -m http.server 8000         # then visit http://localhost:8000
```

There is no linter, formatter, or CI configuration in this repository as of
this writing (verified: no `package.json`, no `.github/workflows`, no config
files present beyond `index.html`, `README.md`, `ARCHITECTURE.md`).

## 14. Release / Distribution Rules

- No version field exists anywhere in the project (no `package.json`, no
  in-app version string). Track releases via git commit/branch only.
- Distribution is simply hosting/serving `index.html` (e.g. any static file
  host, GitHub Pages, or opening the file locally). No packaging, signing,
  or installer step applies.
- No update mechanism exists — a "release" is just deploying a new copy of
  the file to wherever it's hosted.

## 15. High-Risk / Frozen Areas

| System | Why High Risk | Modification Rule |
|---|---|---|
| `localStorage` key names (§9) | Renaming a key silently discards existing users' data with no migration path | Never rename an existing key without writing a migration (read old key, write new key, delete old) |
| `WALLS` video URLs | Hardcoded external CDN URLs the project doesn't own; if a URL changes upstream, that wallpaper silently breaks | Verify new URLs work before adding; don't remove existing entries without checking `mark_wall_src` migration impact for existing users |
| `MAX_BM` (bookmark cap) | Referenced in `renderBookmarks()`, `saveBookmark()`, and implicitly by grid sizing (§17) | If raised, re-check that grid dimension options in the settings panel still make sense |

## 16. Legacy / Dead / Dormant Systems

| System | Status | Why It Exists | Can It Be Removed? |
|---|---|---|---|
| Time-based greeting ("Good morning, `<name>`") | DEAD — removed 2026-08-06 | Was part of the original uploaded version; removed for simplification | Already removed from `index.html`; no trace remains except in git history |
| Task/to-do list feature (`tasks` array, `mark_tasks` storage key, task modal, Bookmarks/Tasks tab bar) | DEAD — removed 2026-08-06 | Was part of the original uploaded version; removed for simplification | Already removed from `index.html`; `.view`/`#content` markup still supports a single active view, a remnant of the old multi-view structure, but only one view (`view-bookmarks`) exists now |
| `ARCHITECTURE.md` (repo root) | CURRENT but superseded in detail | Predates this CodeDoc system | Not dead — still accurate at a high level; keep it in sync or point readers to `CodeDoc/agent.md` for depth |

No `mark_tasks` key is read or written anywhere in current `index.html`; if a
user's browser still has that key from before the removal, it is simply
ignored (dead data, not actively cleaned up).

## 17. Known Architectural Limitations

- Bookmark count is hard-capped at `MAX_BM = 9`, but the grid can be sized up
  to 4 columns × 6 rows (24 slots) via Settings → Bookmark Grid. Choosing a
  large grid with few bookmarks produces mostly empty slots — this is
  current behavior, not a bug, but worth knowing before "fixing" grid math.
- No data sync between devices/browsers — `localStorage` is strictly
  per-browser-profile, per-origin.
- Data export/import is partial (bookmarks + name only, see §6) — this is
  current, intentional-looking behavior but should be confirmed with the
  user before changing.
- All wallpaper and font assets are loaded from third-party URLs at runtime;
  the app has no offline mode and no bundled fallback assets.
- No automated tests exist, so any change must be manually verified in a
  browser (see §1's testing rule and §13).

## 18. Agent Change Checklist

Before editing:
- Read the full `index.html` (it's one file; there's no excuse to skip a
  section).
- Identify which `localStorage` key(s) and JS state variable(s) the change
  touches (§5, §9).
- Check whether the change affects settings-panel "pending vs. applied"
  semantics (§6, §8).

After editing:
- Open `index.html` in a browser (or serve it) and manually exercise: fresh
  onboarding flow (clear the relevant `localStorage` keys or use a private
  window), adding/editing/deleting a bookmark, changing each settings-panel
  control, and export/import.
- Check the browser console for JS errors.
- Update `CodeDoc/history.md` for any meaningful behavior change; update
  this file (`agent.md`) if file structure, ownership, or persisted keys
  changed; update `CodeDoc/human.md` if user-visible behavior changed.

## 19. Documentation Maintenance Contract

Update `agent.md` when: a `localStorage` key is added/renamed/removed, a
major feature (§6) is added or removed, external asset sources (§7) change,
or the file/section structure of `index.html` changes enough to invalidate
§4's line-number map. Do not update it for CSS-only visual tweaks, wording
changes, or new items added to existing lists (e.g. one more wallpaper video)
that don't change the pattern.

CodeDoc accelerates orientation. Current source must still be inspected
before making changes.
