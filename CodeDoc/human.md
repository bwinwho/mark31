# MARK — Human Guide

Project: MARK
Platform: Web browser (works as a personal start page / new-tab-style page)
Document Role: Plain-English guide for the project owner
Last Verified: 2026-09-24
Verified Against: git commit edf74a2 (branch claude/optimistic-goldberg-vucex5)
Current Version Name: Not applicable — this project does not have a version number
Current Version Code/Build: Not applicable
Status: Active

## 1. What This Project Is

MARK is a personal browser start page — the kind of page you'd set as your
new-tab page or homepage. It shows a big live clock over a looping ambient
video, gives you quick access to your most-used bookmarks, and lets you play
soft lo-fi background music while you work. It's built as one plain HTML
file with no account, no server, and no company or brand behind it — it's a
personal tool, not a product with a backend.

## 2. Current Release / State

There is no formal version number for MARK — it's a single evolving file,
tracked by its git history rather than release numbers. The current state
(as of this document) reflects the file after two rounds of simplification
early in the project: an original "greeting" message and a full task/to-do
list feature were both removed to keep the page focused on clock +
bookmarks + wallpaper + music.

## 3. Product Structure

MARK has two main screens:

- **Main screen** — the clock, the video background, and your bookmark grid.
  This is what you see every time you open the page.
- **Settings panel** — slides in from the right when you click "⚙ Settings."
  Everything you can customize lives here.

There's also a small **Add/Edit Bookmark** popup that appears when you add or
edit a bookmark, and a brief **first-visit welcome screen** that asks your
name the very first time you open the page.

## 4. How the Main Experience Works

1. The first time you open MARK, it asks "What should we call you?" You type
   your name and press Enter (or click the arrow). MARK remembers this in
   your browser so you're never asked again on that device/browser.
2. After that, you land straight on the main screen: a large clock, the date,
   a looping video behind everything, and a grid of your bookmarks.
3. Click any bookmark tile to open that site in a new tab. The more you
   click a bookmark, the higher it moves in the grid — your most-used links
   naturally rise to the top.
4. Click "▶ Lo-Fi" in the top corner to start a soft background music
   stream; click again to pause it.
5. Click "⚙ Settings" any time to personalize your clock style, background
   video, music, bookmark grid size, and more.

## 5. Major Features

### Live Clock
A large clock (12-hour format with AM/PM) and a date line, always visible,
updating every 30 seconds. You can change its font and size in Settings.

### Bookmarks
A grid of up to **9 bookmarks**. Click the empty "+" tile to add one — just
give it a name and a URL. Hover over an existing bookmark to reveal small
edit (✎) and delete (×) buttons. Bookmarks automatically re-sort by how
often you click them, so your favorites float toward the front.

### Video Wallpaper
A full-screen looping background video plays behind everything. In Settings
you can pick from 18 preset videos across three moods: **Anime**,
**Landscape**, and **Lo-Fi**. Hover a thumbnail in Settings to preview it
before choosing.

### Lo-Fi Audio
A one-click toggle in the top bar plays a continuous lo-fi music stream in
the background. It's off by default and requires you to click it — browsers
don't allow websites to auto-play sound without a click.

### Settings Panel
One place for all personalization:
- Change your display name.
- Pick one of 8 clock fonts and adjust its size (48–240px) with a slider,
  with a live preview before you apply it.
- Choose your video wallpaper (see above).
- Turn on "Heavy Graphics" — a frosted-glass blur effect on buttons and
  tiles (off by default, since it can affect performance on slower devices).
- Choose your bookmark grid size: 3 or 4 columns, and 3 to 6 rows.
- Export or import your data as a backup file.

## 6. Settings / Controls

| Setting | What It Means | Default/Current Behavior |
|---|---|---|
| Display name | The name MARK remembers for you | Set once during first-visit onboarding; editable anytime in Settings — ON by default (asked upfront) |
| Clock font | Which of 8 typefaces the clock uses | Defaults to "Cinzel" |
| Clock size | How large the clock text is | Defaults to 96px (range 48–240px) |
| Video wallpaper | Which of 18 preset background videos plays | Defaults to "Evil Deity" (Anime category) |
| Heavy Graphics (blur) | Frosted-glass blur effect on UI elements | OFF by default |
| Bookmark grid columns | 3 or 4 columns of bookmark tiles | Defaults to 3 |
| Bookmark grid rows | 3, 4, 5, or 6 rows of bookmark tiles | Defaults to 3 |
| Lo-Fi music | Background ambient audio stream | OFF by default; toggled manually, does not auto-play |

## 7. Data & Internet

- **Local data:** your name, your bookmarks, and all your preference choices
  are stored only in your browser (nothing is uploaded to any server MARK
  controls).
- **Requires internet for:** loading the page's fonts, the background videos,
  bookmark site icons (favicons), and the lo-fi audio stream. These all come
  from public third-party sources on the internet, not from MARK's own
  servers (MARK has none).
- **No login required.** There is no account system at all — "your" data is
  simply whatever is stored in your current browser.
- **No privacy claims beyond this:** nothing you enter is sent anywhere
  except normal, unauthenticated requests to public services (Google Fonts,
  Google's favicon service, the video hosts, and the lo-fi radio stream) —
  the same kind of request any website makes to load an image or a video.

## 8. Offline vs Online

| Feature | Offline? | Behavior Without Internet |
|---|---|---|
| Clock | Yes | Keeps working — it's just your device's local time |
| Bookmarks list & grid | Yes | Your saved bookmarks still show; opening one just fails like any dead link would without internet |
| Video wallpaper | No | Won't load without internet; you'd see a blank/black background |
| Fonts | Partial | Falls back to a plain system font if Google Fonts can't load |
| Lo-Fi music | No | Stream won't play without internet |
| Favicons | Partial | Icon is simply hidden if it can't load; the bookmark still works |

## 9. Fresh Install / First Use

The very first time MARK is opened in a browser (or after clearing that
browser's data for the page), you'll see the welcome screen asking for your
name. Nothing else is configured yet — you'll be on the default clock font
(Cinzel, 96px), the default wallpaper ("Evil Deity"), a 3×3 bookmark grid
with all empty slots except one "+" tile, blur off, and lo-fi music off.
Everything you set afterward is remembered from that point on, in that
browser.

## 10. Data Persistence

MARK remembers everything in your browser's local storage — it survives
closing the tab, closing the browser, and restarting your computer, as long
as you keep using the same browser on the same device and don't clear its
site data.

What can make you lose your data:
- Clearing your browser's cookies/site data for the page.
- Using a different browser or device (MARK doesn't sync between them).
- Reinstalling your browser or resetting your device.

To protect against this, use **Settings → Data → Export** to download a
backup JSON file periodically, and **Import** to restore it later (note:
Import currently restores only your name and bookmarks — it does not
restore your clock style, wallpaper, or grid size choices, so you'd need to
reset those manually after an import).

## 11. Current Features by Status

**CORE** (always available, no toggle needed):
- Live clock
- Bookmark grid
- Video wallpaper
- Settings panel
- Data export/import

**EXPERIMENTAL / OPTIONAL** (off by default, opt-in):
- Lo-Fi background music
- Heavy Graphics (blur effect)

No features are currently LOCKED or DORMANT.

## 12. Removed / Deprecated Features

Two features that existed in the very first uploaded version were removed
shortly after, on the same day the project was uploaded:

- **Greeting message** ("Good morning/afternoon/evening, `<your name>`.")
  that used to appear above the clock. Removed to simplify the main screen.
- **Task / to-do list feature**, including a task-add popup and a tab bar to
  switch between "Bookmarks" and "Tasks" views. Removed to keep the app
  focused purely on the clock + bookmarks + wallpaper experience.

Neither feature is present in the current app, and there's no setting to
bring them back — they were deleted from the code, not just hidden.

## 13. Known Limitations

- You can size your bookmark grid larger than the 9-bookmark limit (e.g. a
  4×6 grid gives 24 slots), which just leaves extra tiles empty — the
  9-bookmark cap doesn't grow with the grid.
- No syncing between devices or browsers — it's local to wherever you use it.
- Backup/restore (Export/Import) doesn't currently carry over your clock
  style, wallpaper choice, blur setting, or grid size — only your name and
  bookmarks.
- The background videos, fonts, and music stream depend on outside services
  staying online; MARK doesn't host any of that content itself.

## 14. Release Basics

MARK isn't distributed as a packaged app — it's a single HTML file. "Using
the latest version" simply means opening the most recently updated copy of
`index.html`, wherever it's hosted or saved. There's no install process, no
app store, and no automatic update mechanism; a new version is just a new
copy of the file.

## 15. Project Vocabulary

| Term | Meaning |
|---|---|
| MARK | The name of this project/page |
| Bookmark grid | The tile layout on the main screen showing your saved links |
| Video wallpaper | The looping background video behind the whole page |
| Heavy Graphics | The optional frosted-glass blur visual effect |
| Onboarding | The one-time "what should we call you?" welcome screen |
| Settings panel | The slide-in panel (via the ⚙ button) holding all customization options |

## 16. Where To Look For More Detail

Technical architecture: `CodeDoc/agent.md`

Project history: `CodeDoc/history.md`
