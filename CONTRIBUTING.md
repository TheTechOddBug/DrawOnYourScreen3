# Contributing to Draw On Gnome

Thank you for your interest in contributing to **Draw On Gnome**! This document outlines the guidelines and process for submitting contributions.

Have questions or want to discuss ideas first? Start a [Discussion on GitHub](https://github.com/daveprowse/Draw-On-Gnome/discussions/categories/ideas).

---

## Version Overview

Draw On Gnome currently has two active versions:

| Version | GNOME Support | Status |
|---------|--------------|--------|
| **v9.0** | GNOME 46–49 | Frozen — maintenance only |
| **v11.0+** | GNOME 50+ | Active development |

**All new development targets GNOME 50+.** If you are contributing a new feature, it belongs in the v11.0+ codebase only. 

PRs for v9.0 will not be accepted unless they address a critical bug or security issue. To do this, please work in the `version-9` branch!

> Note: fixes to version-9 are maintained separately and will not be merged into main."

---

## Before You Start

Please check the [Roadmap](https://daveprowse.github.io/Draw-On-Gnome/roadmap/) to see what is already planned, in progress, or being discussed. If your idea is already listed, feel free to take a shot at it and open a PR. If it is something new, open a Discussion first so we can talk through the approach before you invest time writing code.

---

## Testing Requirements

Draw On Gnome v11.0+ targets **GNOME 50 and newer**. Please test your code on GNOME 50 before submitting a PR, and note clearly which versions you tested in your PR description.

### Why This Matters

- Ubuntu 26.04 LTS ships GNOME 50 — a major user base
- Fedora 44 ships GNOME 50
- GNOME 50 is Wayland-only — X11 compatibility code is no longer relevant

### GNOME 50 Specifics

- **`Meta.Cursor` and `display.set_cursor()` are gone** — cursor shape changes are currently non-functional on GNOME 50 (no public replacement API exists for extensions). Do not attempt to reintroduce these calls.
- **`Clutter.Color` is gone** — use `Cogl.Color` or the existing `Color` ternary pattern in `elements.js`
- **Wayland-only** — do not introduce any X11-specific code paths

---

## Code Guidelines

### No GTK or GDK in Main Extension Files

The extension runs in the GNOME Shell process. GTK and GDK are **not available** here and will cause immediate crashes. These libraries are only permitted in:

- `prefs.js`
- Files inside the `ui/` directory

Main extension files (`area.js`, `areamanager.js`, `helper.js`, `menu.js`, `elements.js`, etc.) may only use:

```javascript
import Clutter from 'gi://Clutter';
import St from 'gi://St';
import GObject from 'gi://GObject';
import GLib from 'gi://GLib';
import Meta from 'gi://Meta';
import Shell from 'gi://Shell';
// etc.
```

### Logging

Follow the [GJS Extension Port Guide for GNOME 45+](https://gjs.guide/extensions/upgrading/gnome-shell-45.html#logging):

- **Never** use bare `log()` calls
- Use `console.debug()` for debug messages (ideally remove before submitting)
- Use `console.warn()` for warnings
- Use `console.error()` for errors
- Keep `logError(e)` for caught exceptions — this is correct and acceptable

### Clean Up After Yourself

If your contribution adds new widgets, signal handlers, keybindings, or other resources, make sure they are properly destroyed or disconnected in the appropriate `disable()` or `destroy()` method. Memory leaks and lingering handlers will cause issues for users.

### Schema Files

The extension uses three separate GSettings schema files in `schemas/`:

- `org.gnome.shell.extensions.draw-on-gnome.gschema.xml`
- `org.gnome.shell.extensions.draw-on-gnome.drawing.gschema.xml`
- `org.gnome.shell.extensions.draw-on-gnome.internal-shortcuts.gschema.xml`

If you add a new GSettings key, add it to the appropriate schema file and recompile locally with:

```bash
glib-compile-schemas schemas/
```

Include the updated `gschemas.compiled` in your PR — it is required for GitHub installs. Do **not** include it if submitting directly to the GNOME Extensions store (E.G.O. compiles schemas itself).

---

## Submitting a Pull Request

1. Fork the repository and create your branch from `main`
2. Make your changes
3. Test on GNOME 50
4. In your PR description, include:
   - What the change does
   - Which GNOME versions you tested on
   - Any known limitations or areas that need further testing
5. Keep PRs focused — one feature or fix per PR where possible

---

## What Happens After You Submit

Pull requests are evaluated and tested on GNOME 50 before merging. This takes time. The maintainer may:

- Merge your PR as-is
- Merge a modified version of your PR (e.g. reverting a specific change that conflicts with existing behavior) and close the original PR with a note
- Request changes via review comments
- Close the PR if it conflicts with the project direction

Please be patient — this is a one-person maintainer project.

---

## License

By contributing, you agree that your code will be licensed under the [GNU General Public License v3 or later](https://www.gnu.org/licenses/gpl-3.0.html), consistent with the rest of the project.

---

Thanks again for contributing. Every improvement helps the community of GNOME users who rely on this tool! 🎨