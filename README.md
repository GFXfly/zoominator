# Zoominator – Smart Scene Zoom & Follow for OBS Studio

[![Watch the demo](https://img.youtube.com/vi/R3KQnsnzIAM/0.jpg)](https://www.youtube.com/watch?v=R3KQnsnzIAM)

Zoominator is an OBS Studio plugin that dynamically **zooms and pans the entire scene** to follow your mouse, keeping the focus exactly where the action happens.

It operates at the **scene level**, meaning it works with any source automatically no per-source setup, no dock, no complexity.

---

## What It Does

- **Scene-wide Zoom & Pan**  
  Smoothly transforms the entire scene based on mouse position.

- **Smart Clamping**  
  Ensures the canvas is always fully covered no black edges, even with cropped sources.

- **Mouse-Driven Focus**  
  Choose where the zoom is anchored: the canvas center, the cursor position at
  trigger time (static), or the cursor position with continuous following.

- **Ultrawide Edge Tracking**
  Optionally keeps the cursor centered until the captured scene reaches an edge.

- **Idle Freeze**
  Pauses mouse following after a configurable idle timeout and resumes on movement.

- **Click Highlight (Halo)**  
  Optional click-only visual feedback using a configurable halo.

- **Flexible Activation**  
  Toggle or hold behavior with customizable key combinations.

- **Wheel Zoom (Windows and X11)**
  With Mouse X2 in toggle mode and no modifiers, hold the button and scroll to
  change zoom. Release the button to keep the selected level, or click it
  without scrolling to reset. The plugin consumes wheel events during the
  gesture so the application below the pointer does not scroll.

---

## Installation

> **Requirements:** Zoominator 2.1.0 and newer are built against **OBS Studio 33** and
> need OBS 33.0 or later. On OBS 31 / 32 keep using
> [Zoominator 2.0.6](https://github.com/mmlTools/zoominator/releases/tag/2.0.6).

### Windows

1. Download the latest release.
2. Extract the archive.
3. Copy the **contents of the extracted archive** to your OBS Studio installation directory, preserving the directory structure.

   The release package contains both the plugin binary and required localization files. Do **not** copy only `zoominator.dll`.

4. Restart OBS.

> **Troubleshooting:** If Zoominator appears in the OBS `Tools` menu as `Menu.Tools.Zoominator` instead of `Zoominator`, the required localization files may not have been installed. Reinstall the plugin using the complete release archive.

### macOS
1. Download the `.pkg` or `.dmg` from releases
2. Install and restart OBS

### Linux (X11)
1. Build from source or install via package (if available)
2. Copy plugin files into:
   ```
   ~/.config/obs-studio/plugins/
   ```
3. Restart OBS

---

## Build from Source

The build follows the official OBS plugin template: `buildspec.json` pins the OBS
sources and the pre-built dependencies (currently OBS 33.0.0 and obs-deps
2026-10-23), and CMake downloads and verifies them on first configure.

### Requirements
- CMake 3.28 or newer
- **Windows:** Visual Studio 2026 with the Windows 11 SDK 10.0.26100
- **macOS:** Xcode 26.5 or newer (macOS 13.0 deployment target, universal binary)
- **Linux:** `libobs-dev`, Qt 6 development packages, X11 / Xrandr / XInput2 headers

### Steps
```bash
git clone https://github.com/mmlTools/zoominator.git
cd zoominator

# Windows
cmake --preset windows-x64
cmake --build --preset windows-x64 --config RelWithDebInfo

# macOS
cmake --preset macos
cmake --build --preset macos

# Linux
cmake --preset ubuntu-x86_64
cmake --build --preset ubuntu-x86_64
```

The built plugin and its `data/` folder land in `build_<arch>/rundir/<config>/`.
Copy `zoominator.dll` to `<OBS>/obs-plugins/64bit/` and the `zoominator` data folder
to `<OBS>/data/obs-plugins/` to test it locally.

### Releasing (maintainers)

CI never builds on a plain push; builds are started by hand and the tag reuses them.

1. Bump `version` in `buildspec.json`, commit and push to `main`.
2. In GitHub **Actions → Build → Run workflow**, pick `Release` and run it.
   The run summary prints the exact `git tag` command for that commit.
3. Tag the built commit with the same version and push the tag:
   ```bash
   git tag 2.1.0 <commit>
   git push origin 2.1.0
   ```
4. The **Push** workflow finds the successful Build run for that commit, downloads
   its artifacts and attaches them, with checksums, to the GitHub release
   (created as a draft if it does not exist yet). Nothing is rebuilt. If no Build
   run exists for the tagged commit, it builds once as a fallback.

The tag must match the `buildspec.json` version; the workflow fails otherwise.

## Compatibility Notes

- **OBS Studio:** 33.0 or newer (Zoominator 2.1.0+). Use 2.0.6 on OBS 31 / 32.
- **Windows:** Full support (global input, smooth tracking, and Mouse X2 + wheel zoom)
- **macOS:** Requires Accessibility permissions for input tracking
- **Linux (X11):** Supported via XInput2
- **Wheel zoom:** Supported on Windows and Linux X11. Zoom-in and zoom-out sensitivity, minimum and
  maximum zoom, and animation durations are configurable in the Advanced tab.
- **Wayland:** Native sessions are detected and X11 hooks are disabled. The
  Global Shortcuts portal can support hotkeys, but Wayland currently has no
  standard passive global cursor-position portal, so full mouse tracking still
  requires compositor-specific input capture support.

---

## Use Cases

- Tutorials & live coding  
- Product demos  
- Gameplay & analysis  
- Vertical / short-form content  

---

## Notes

Zoominator behaves like a **virtual camera system inside OBS**, applying transformations at the scene level for maximum flexibility and reliability.

## Clang format fix

```bash
git add --chmod=+x build-aux/.run-format.zsh build-aux/run-clang-format build-aux/run-gersemi build-aux/run-swift-format build-aux/.functions/*
git add --renormalize .
git commit -m "Make build scripts executable and normalize line endings"
```

```gitattributes
.gitattributes
*.sh text eol=lf
*.zsh text eol=lf
.github/scripts/** text eol=lf
.functions/* text eol=lf
run-clang-format text eol=lf
run-gersemi text eol=lf
run-swift-format text eol=lf
.run-format.zsh text eol=lf
```

```bash
git add --renormalize .
git commit -m "Normalize line endings for Unix scripts"
git push
```
