# A-Team Android Image Toolkit

Cross-platform GTK3 Android image and device toolkit for Windows and Linux.

## Modules

1. **A-Team Sparse Chunk Converter** — sparse chunk -> raw `super.img`.
2. **A-Team Super Image Extractor** — `lpunpack` based dynamic partition extraction.
3. **A-Team EROFS / EXT4 Image Rebuilder** — supplied unpack/repack scripts (Linux-oriented).
4. **A-Team Super Image Creator** — dynamic `super.img` creator.
5. **A-Team Vendor Boot Extract / Rebuild** — vendor_boot ramdisk extraction/rebuild.
6. **A-Team Vbmeta Security Disabler** — supplied vbmeta flashing workflow.
7. **A-Team Android Image Kitchen** — supplied Windows/Linux image kitchen.
8. **A-Team Terminal** — command console with copyable output.

## Device controls

The main window exposes quick buttons for:

- `adb devices`
- `adb reboot`
- `adb reboot recovery`
- `adb reboot bootloader`
- `fastboot devices`
- `fastboot reboot`
- `fastboot reboot recovery`
- `fastboot oem unlock`
- `fastboot flashing unlock`

Unlock commands require confirmation because bootloader unlocking can erase user data.

## Project layout

- `src/` — application source (`core/` and `gui/`)
- `modules/` — toolkit modules and their bundled tools
- `scripts/` — launcher, Windows bootstrap, hidden launcher, and update helper
- `tools/` — bundled command-line tools; development helpers are under `tools/dev/`
- `config/` — Python dependency requirements
- `assets/` — application artwork
- `Project_Working_Folders/` — persistent per-module working directories

The root directory is intentionally kept for user-facing launchers, documentation, licensing, and project metadata.

## Platform Tools

The toolkit bundles the Android platform tools it needs in:

- Linux: `platform-tools/linux/`
- Windows: `platform-tools/windows/`

The GUI and modules automatically select the correct bundled tools for the host platform from `platform-tools/<platform>/`, with a PATH fallback where appropriate. Shared image tools (`simg2img`, `img2simg`, `lpunpack`, `lpmake`, and `mke2fs`) are kept in this single canonical location so modules do not carry duplicate copies.

Official Android Platform-Tools information: https://developer.android.com/tools/releases/platform-tools

## GTK3 prerequisites

Linux typically needs GTK3 + PyGObject, for example on Debian/Ubuntu:

```bash
sudo apt install python3-gi gir1.2-gtk-3.0
```

Windows can use an existing Python distribution with GTK3/PyGObject. If `gi` is not available, the Windows launcher automatically prepares the MSYS2 UCRT64 GTK3/PyGObject runtime (using an existing MSYS2 installation or winget when available) and then launches the same application code. Linux behavior is unchanged.

## Launch

Linux:

```bash
./START_LINUX-A-Team-Android_Image_Toolkit.sh
```

Windows:

```bat
START_WINDOWS-A-Team-Android_Image_Toolkit.bat
```

The launcher and supporting bootstrap/update scripts live under `scripts/`; they are implementation details and normally do not need to be run directly. The application source lives under `src/` and is loaded from there by the launchers.

The architecture is module-first: each directory under `modules/` contains a manifest and `module.py`, so future A-Team packages can be added without changing the application shell.

## Magisk Boot Patcher

Module 08 presents the supplied A-Team Magisk 0.07 patcher through the same file/browse/options/log workflow used by the Super Image Creator. The supplied patcher binaries are Linux ELF and are therefore enabled on Linux; the original source is retained unchanged inside the module.

## Integrated updater

The Device Tools header includes **Check for Updates**. The updater reads the platform-specific metadata from `Updates/`, compares the published `display` version with the installed toolkit version, and can download/install the referenced update package. It tries the A-Team Forgejo server first and automatically falls back to the GitHub mirror at `https://github.com/PizzaG/A-Team-Android_Image_Toolkit` if Forgejo is unavailable. An optional `sha256` field can be supplied in the JSON for archive verification before installation. The update prompt is opened as a separate popup window.

Expected update metadata fields:

```json
{
  "version": "2",
  "display": "1.3.1",
  "file": "A-Team-Android_Image_Toolkit-v1.3.1.7z",
  "changelog": "Release notes here",
  "sha256": "optional sha256 hex digest"
}
```
