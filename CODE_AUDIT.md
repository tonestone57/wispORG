# Full Code Audit & Analysis Report

## 1. Summary of Project Status
**Project:** Wisp (C/C++ browser fork of Netsurf)

**Status:** In development. Modernizing Netsurf for the modern web.

**Key Known Issues (from README):**
- JS engine (QuickJS) is just a stub.
- Frontends other than Qt (Linux) and GDI (Windows) are untested and probably don't work.
- Compatibility dropped for old libraries/OSes in favor of modern implementations.
- Removed compatibility for super old and/or obscure libraries/software/operating systems
- Dedicated LibreSSL support
- Numerous privacy improvements
- Rewritten build system
- Simplified frontend development

## 2. Summary of Outstanding Tasks
I have scanned the codebase for FIXMEs, TODOs, and XXXs.
- **Total TODOs/XXXs found:** 904
- **Total FIXMEs found:** 14

## 3. High-Priority Tasks (FIXMEs)
- **Contrib (quickjs-ng):**
  - `contrib/quickjs-ng/quickjs.c:6900` - FIXME: (bnoordhuis) Missing `sf->cur_pc = pc` in bytecode
  - `contrib/quickjs-ng/quickjs.c:25381` - FIXME: (bnoordhuis) less stack shuffling; don't to_propkey twice in
  - `contrib/quickjs-ng/quickjs-libc.c:2631` - FIXME: (bnoordhuis) the loop below is quadratic in theory but
- **Frontends (beos):**
  - `frontends/beos/gui.cpp:1087` - FIXME: use options as read only for replicants
  - `frontends/beos/gui.cpp:1091` - FIXME: must not die when in replicant!
  - `frontends/beos/gui.cpp:1103` - FIXME: must not die when in replicant!
  - `frontends/beos/scaffolding.cpp:270` - FIXME: this flickers on resize, quite ugly
  - `frontends/beos/scaffolding.cpp:1765` - FIXME: This is supposed to be a popup menu!
- **Frontends (gtk):**
  - `frontends/gtk/print.c:62` - FIXME: <no description>
  - `frontends/gtk/compat.c:329` - FIXME: <no description>
  - `frontends/gtk/compat.c:379` - FIXME: <no description>

## 4. General Task Categories (TODOs/XXXs)

### Frontends
- **Qt (Linux):** Needs handling of reverse layout, checking page parent scaffolding, color management, and fixing `qobject_cast` failing in `actions.cpp`.
- **Windows (GDI):** Needs work on why scroll needs scaling, win32 clear operation deleting contents, checking URL escaping of paths, and vertical scrolling calling set/get scroll appropriately. The Windows download functionality is noted as very buggy and needing redoing.
- **Other Untested Frontends:** BEOS, GTK, ViSurf have numerous TODOs for UI functionality, memory management, toolbar sensitivity, plotting, and handling events.

### Core Engine
- **CSS / Layout:** Many TODOs related to handling `@import`, voice-family properties, box model adjustments, extending nested block handling, and media list.
- **JS / DOM:** Missing bindings, system ID fixing in xml parser, stubbed QuickJS engine functionality needs expanding.
- **Networking / Fetch:** Needs implementation of v6 address support, handling HTTP RFC2047 encoding and quoted-pairs, caching improvements (LLCache and HLCache string comparison avoiding, handling duplicate headers like Set-Cookie).
- **Utils:** IDNA compliance checking, utf-8 whitespace string squashing testing, file path handling in OS abstractions.
