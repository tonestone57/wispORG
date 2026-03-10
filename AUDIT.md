# Wisp Codebase Audit & Status Report

This document contains a full audit and analysis of the Wisp codebase, summarizing the current state of development, active plans, and pending TODOs. The codebase contains approximately 148 active TODO/FIXME markers spanning core HTML/CSS rendering, JavaScript integration, frontends, and utility modules.

## 1. Summary of Current State

Wisp is actively transitioning to modern web standards, with significant works-in-progress spanning layout engine refinement, JavaScript integration via QuickJS, UI restructuring (Windows tabbed interface), and core asset loading capabilities (fonts).

Key areas of ongoing work:
- **JavaScript / QuickJS Integration**: QuickJS is currently just a stub. Significant effort is planned to port from the old Duktape bindings to QuickJS, requiring transpilation and the addition of modern DOM APIs (e.g., `querySelector`).
- **HTML Rendering & Layout**: The layout engine is functional but lacks support for several CSS edge cases, particularly regarding Flexbox/Grid constraints, Tables, and specific Box constructs.
- **Windows Frontend**: A major architectural UI rework is planned to transition from a 1:1 window-per-browser-context model to a tabbed "scaffold" architecture matching the Qt frontend.
- **CSS Styling & Font Variant Loading**: Fixes are needed for font variant loading (weight/style) to prevent duplicate fetches and properly pass variant metadata to the rendering frontends.

---

## 2. Details of Pending Work

### 2.1 JavaScript / QuickJS Integration (Pending Modernization)
- **Status:** Unfinished stub. Event handling and core JS<->DOM bindings are missing.
- **Outstanding Tasks:**
  - `src/content/handlers/javascript/quickjs/location.c`: `Implement actual reload`
  - `src/content/handlers/javascript/quickjs/event_target.c`: `Store listener for later dispatch`
  - `src/content/handlers/javascript/quickjs/quickjs_bindings.c`: `Implement event firing`, `Implement event listener registration`, `Implement new element handling`, `Implement event cleanup`
  - **Planned Action:** Execute `plans/js_migration_analysis.md` (Transpiler for `.bnd` to QuickJS + missing features).

### 2.2 HTML Rendering & Layout (Core Engine Refinement)
- **Status:** Functional, but incomplete in edge cases (e.g., Flexbox/Grid constraints, Tables, specific Box constructs).
- **Outstanding Tasks (~88 TODOs):**
  - **Box Model / Construction:** Need implementation for `counters`, `proper quote handling with nesting level`, `CSS_COMPUTED_CONTENT_URI for images`.
  - **Grid/Flexbox Layout:** Handle indefinite context in grid (`src/content/handlers/html/layout_grid.c`); handle percentage constraints properly (`src/content/handlers/html/layout_internal.h`, `src/content/handlers/html/layout.c`).
  - **Table Layout:** Handle `colspan="0"` correctly.
  - Fix similar constraint layout issue in `src/content/handlers/html/layout.c:3438` per `plans/commit-d59da0b3-review.md`.

### 2.3 Windows Frontend UI Rework (Tabbed Interface)
- **Status:** Well-planned, implementation not yet started/completed.
- **Outstanding Tasks:**
  - Execute `plans/windows-ui-rework-plan.md` with improvements from `plans/windows-ui-rework-assessment.md`.
  - Requires writing `scaffold.h/c`, `actions.h/c`, modifying `window.c/h`, handling tab close buttons natively, and updating resource files.
  - Address minor TODOs in `frontends/windows/plot.c` (pixel alignment) and `frontends/windows/window.c` (redraw vs reformat).

### 2.4 CSS Styling & Font Variant Loading
- **Status:** Planned, awaiting implementation.
- **Outstanding Tasks:**
  - Execute `plans/font-variant-loading-plan.md`.
  - Fix dedup and carry variant info through `src/content/handlers/html/font_face.c`.
  - Update `load_font_data` interface in `include/wisp/layout.h`.
  - Update `frontends/qt/layout.cpp` and `frontends/windows/font.c`.
  - Resolve outstanding CSS parser TODOs in `src/content/handlers/css/select.c` (e.g., `Handle first-line and first-letter pseudo`).

---

## 3. Categorized Task List

Here is the task list organized by category, prioritized by impact and feasibility based on the current active plans.

### Category A: Planned Architectural Migrations (High Priority)

#### Task 1: Font Variant Loading Fix (`plans/font-variant-loading-plan.md`)
- Update `struct font_download` and `loaded_font` to include weight/style.
- Implement `is_variant_loaded` and `is_variant_pending` in `font_face.c`.
- Thread variant info through `html_font_face_process`, `fetch_font_url`, and `font_fetch_callback`.
- Update frontend interface in `layout.h` and implement in Qt and Windows frontends.

#### Task 2: Windows Tabbed UI Rework (`plans/windows-ui-rework-plan.md`)
- Create `gui_scaffold` and update `gui_window`.
- Implement `scaffold.c` (Tab Control, Toolbar, Layout).
- Implement `actions.c` (Menu and commands).
- Implement tab close buttons (Custom drawing approach recommended by assessment).
- Wire browser callbacks to the new scaffold.

#### Task 3: QuickJS Migration Prep (`plans/js_migration_analysis.md`)
- Fix immediate issues in Duktape reference files (e.g., `Document.bnd` args parsing, `EventTarget.bnd` deduplication).
- Implement QuickJS DOM transpiler script.
- Flesh out missing QuickJS stub functionality (`event_target.c`, `quickjs_bindings.c`).

### Category B: Layout & Rendering Correctness

#### Task 4: Layout Pass Fixes
- Address the layout min/max constraints issue in `src/content/handlers/html/layout.c:3438` (using proper max-height/width instead of `INT_MAX`).
- Implement percentage width handling across block/flex/grid layouts.
- Address Table layout spanning issues and `colspan="0"`.

#### Task 5: Box Tree / CSS Features
- Add support for CSS counters.
- Implement `first-line` and `first-letter` pseudo-elements.
- Support `content: url()` for images in pseudo elements.

### Category C: General Cleanups

#### Task 6: Codebase TODO cleanup
- Address minor cleanup TODOs across `src/content/handlers/html/html.c` and `src/content/handlers/html/css.c`.
- Remove production warnings (e.g., `// TODO remove in production` in `src/content/handlers/html/redraw.c`).
