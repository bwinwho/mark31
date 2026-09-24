# MARK — Project History

Project: MARK
Platform: Static web (client-side browser page)
Document Role: Chronological project memory (decision log + changelog + release history)
Last Verified: 2026-09-24
Verified Against: git commit edf74a2 (branch claude/optimistic-goldberg-vucex5), full git log
Current Version Name: Not applicable
Current Version Code/Build: Not applicable
Status: Active

## How To Read This File

- Newest entries first.
- Only meaningful changes are recorded here (features, removals, architecture
  decisions, releases) — not every commit or tiny edit.
- This file is append-only: superseded decisions get a new entry, old
  entries are never deleted or rewritten.
- Current architecture lives in `CodeDoc/agent.md`.
- Current product behavior lives in `CodeDoc/human.md`.
- This file explains how and why the current state evolved.

## Current Milestone

Current version: Not applicable (no version numbering scheme exists)
Date: 2026-09-24 (CodeDoc system introduced)
Current product direction: A deliberately minimal, single-file personal
browser start page — clock, bookmarks, video wallpaper, lo-fi audio, and a
settings panel — after an early simplification pass removed a greeting
message and a task/to-do list feature.

## Timeline

### 2026-09-24 — CodeDoc Memory System Introduced

**Area:** Project documentation

**Changed**
- Added `CodeDoc/agent.md`, `CodeDoc/human.md`, and `CodeDoc/history.md` as
  the project's structured long-term memory system, built by inspecting the
  current `index.html` and full git history.

**Why**
- To give future AI agents and the human owner a compact, accurate reference
  that doesn't require re-reading the entire commit history or re-deriving
  architecture from scratch each time.

**Result**
- Three new files under `CodeDoc/`. Existing `README.md` and
  `ARCHITECTURE.md` were left in place, unmodified, as they remain accurate
  at a high level.

**Files / Systems**
- `CodeDoc/agent.md`, `CodeDoc/human.md`, `CodeDoc/history.md` (new)

**Verification**
- Manual review of `index.html` (full file), `README.md`, `ARCHITECTURE.md`,
  and `git log` with diffs for all prior commits.

**Status**
- SHIPPED

---

### 2026-08-27 08:39 — Add README and ARCHITECTURE Documentation

**Area:** Project documentation

**Changed**
- Added `README.md` (user-facing overview: what the app does, how to run it,
  data/storage notes, project structure) and `ARCHITECTURE.md` (technical
  overview: layers, state, persistence keys, data flow, external resources).

**Why**
- The project had no documentation up to this point beyond its source code.

**Result**
- Two new root-level Markdown files describing the app as it existed after
  the greeting/task removal below (i.e. clock + bookmarks + wallpaper +
  lo-fi + settings, no greeting, no tasks).

**Files / Systems**
- `README.md`, `ARCHITECTURE.md` (new)

**Verification**
- Not applicable (documentation-only commit).

**Status**
- SHIPPED

---

### 2026-08-06 09:44 — Remove Task Management Features

**Area:** Bookmarks/Tasks view system

**Changed**
- Removed the entire task/to-do list feature: the `tasks` state array, the
  `mark_tasks` localStorage key, `renderTasks()`, `saveTasks()`,
  `openTaskModal()`/`closeTaskModal()`, `saveTask()`, `toggleTask()`,
  `deleteTask()`, the task modal markup, the `#tasks-list` view, the
  bottom tab bar (`renderBottomBar()`, `switchView()`, `currentView` state),
  and all related CSS (`.task-item`, `.task-check`, `.tasks-empty`,
  `.plus-btn`, `.tab-btn`, `#bottom-bar`).
- Net diff: 126 lines removed, 8 added (mostly whitespace/formatting cleanup
  left behind by the removal).

**Why**
- Product decision to simplify the app down to a single-purpose bookmark +
  clock + wallpaper start page rather than a combined bookmarks/tasks tool.

**Result**
- The app now shows only the bookmarks view; there is no way to switch views
  or manage tasks. The `.view`/`#content` wrapper markup still technically
  supports multiple views structurally, but only `view-bookmarks` exists.

**Files / Systems**
- `index.html` (all layers: CSS, markup, JS)

**Verification**
- Not applicable at the time (no test suite exists in this project, then or
  now); verified retroactively via `git show --stat` and full diff review
  during CodeDoc creation (2026-09-24).

**Status**
- SHIPPED

---

### 2026-08-06 09:43 — Remove Greeting and Task Modal Sections

**Area:** Clock section / Task modal

**Changed**
- Removed the `#greeting` element and its CSS (the "Good morning/afternoon/
  evening, `<name>`." message shown above the clock) and the `renderGreeting()`
  function/call sites.
- Removed the standalone task-add modal (`#task-modal`) markup (a separate,
  smaller removal that preceded the full task-feature removal in the next
  commit).
- Net diff: 14 lines removed.

**Why**
- First step of the same simplification effort completed in the following
  commit (task management removal) — greeting and one piece of task UI were
  removed first, then the rest of the task system in the very next commit
  a few minutes later.

**Result**
- The clock section shows only the clock and date line, no greeting text.

**Files / Systems**
- `index.html`

**Verification**
- Not applicable at the time; verified retroactively during CodeDoc
  creation.

**Status**
- SHIPPED (superseded/completed by the following commit, which removed the
  remaining task-system code)

---

### 2026-08-06 09:40 — Add Files via Upload (Initial Application Code)

**Area:** Whole application

**Changed**
- Uploaded the initial `index.html` (909 lines) containing the full original
  version of the app: clock, greeting, bookmarks, a task/to-do list with a
  Bookmarks/Tasks tab bar, video wallpaper, lo-fi audio, and the settings
  panel.

**Why**
- Initial import of the working application into this repository.

**Result**
- First functional version of MARK in git history, later simplified by the
  two removal commits above (both on the same day).

**Files / Systems**
- `index.html` (new, 909 lines)

**Verification**
- Not applicable (initial upload).

**Status**
- SUPERSEDED (by the two removal commits later the same day)

---

### 2026-08-06 09:39 — Initial Commit

**Area:** Repository setup

**Changed**
- Repository created with a minimal 2-line `README.md`.

**Why**
- Project start.

**Result**
- Empty-ish repository scaffold, before the application code was uploaded
  minutes later.

**Files / Systems**
- `README.md` (initial, later replaced by the fuller version on 2026-08-27)

**Verification**
- Not applicable.

**Status**
- SUPERSEDED (README later rewritten on 2026-08-27)
