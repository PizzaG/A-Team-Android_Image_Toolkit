## v1.5
- Fixed updater version handling to match the proven Jarvis model: `version` is now the integer machine release code while `display` remains the human-facing version.
- Added a metadata consistency guard so a mismatched/stale record (for example `version=16` with `display=1.4`) can never be offered as an update over installed v1.5.
- Fixed Module 01 sparse chunk folder selection on Linux and Windows. The module now selects the folder containing the sparse chunks and discovers the chunk files directly instead of relying on a multi-file picker or shell globbing.
- Moved the update-available prompt into a separate top-level popup window so it no longer renders inside the module pane.
- Restored the updater to the single `Updates/version.json` metadata format.
- Removed DEB/EXE package update handling; both platforms use the same `.7z` update archive.
- Added GitHub as a backup update server for Forgejo outages, using the same `Updates/version.json` and package layout.
- Consolidated shared image-processing binaries into `platform-tools/<platform>/` and updated module resolution so Linux and Windows modules use the single canonical copy.
- Public Release v1.5

## v1.4
- Added Windows EXE packaging support with a self-contained application build and installer workflow.
- Added Linux DEB packaging support with a standard desktop application entry.
- Added platform-specific settings.ini configuration for the Project_Working_Folders path, defaulting to the user's home directory on Linux.
- Added optional sudo password configuration through settings.ini.
- Added platform-aware updater handling for extracted 7z, Windows EXE, and Linux DEB installations.
- Added separate update metadata support for extracted.json, windows.json, and linux.json.
- Added Windows EXE and Linux DEB update installation handling.
- Added reusable Linux and Windows package builder scripts.
- Preserved the cleaned project source layout while retaining existing application and module functionality.
- Public Release v1.4

## v1.3
- Restored Linux ownership and writable permissions for `app_log.txt` when the toolkit runs elevated, preventing the desktop user from being left with a locked/root-owned log.
- Re-asserted log permissions safely during logging so the fix survives new or replaced log files.
- Preserved existing Linux working-folder permissions and behavior.
- Public Release v1.3

## v1.2.28
- Module 3: route non-terminal progress through the existing GTK progress bar.
- Module 3: strip ANSI colors/cursor control sequences and suppress terminal block-bar redraws in GUI output.
- Module 3: retain normal interactive terminal progress behavior on Linux.
- Module 3: keep harmless `tput: unknown terminal` diagnostics suppressed in GUI/non-terminal runs.

## v1.2.27
- Suppressed harmless Linux EROFS/EXT4 rebuilder `tput: unknown terminal "unknown"` diagnostics when launched from the GTK GUI, while preserving interactive terminal behavior.

## v1.2.26
- Removed the redundant Module 6 Operation dropdown; Unpack Image, Repack Image, and Cleanup buttons now directly select the operation.
- Fixed Windows Module 6 command construction so the complete GUI-safe command is passed to cmd.exe as one /c command string.
- Applied the corrected Windows invocation to unpack, repack, and cleanup.

## v1.2.24
- Fixed Module 6 Windows batch invocation so quoted Android Image Kitchen script paths are passed correctly through cmd.exe.
- Applied the corrected GUI-safe command invocation to unpack, repack, and cleanup operations.

## v1.2.23
- Fixed Module 5 Windows rebuild publishing when the selected output image is inside the staged working folder. The rebuilt image is now published only after the working folder is restored.
- Made Module 5 ownership restoration explicitly Linux-only; Windows no longer attempts Unix `os.chown()` handling.

## v1.2.21
- Module 5 Windows vendor_boot extraction now automatically installs/verifies the required native `lz4` Python module in the MSYS2 UCRT64 environment before running the extractor.
- Linux Module 5 behavior is unchanged.

## v1.2.20
- Fixed Windows Module 4 crash caused by Unix-only os.geteuid() ownership restoration.
- Added Module 4 subprocess/stage logging to app_log.txt.
- Clarified lpmake sparse-probe messages in the Module 4 output.

## v1.2.17
- Fixed Windows EROFS TAR rebuilding against an erofs-utils 1.8.x bug that can produce a bogus ~2 TiB image when TAR paths traverse intermediate symlinks. Intermediate symlink components are now resolved before the TAR is handed to mkfs.erofs.
- Added explicit EROFS TAR preparation statistics and clearer extraction-stage output.
- Added reliable application-wide session logging of module output and subprocess activity to app_log.txt.

## v1.2.14
- Windows Module 3 now reuses an existing non-empty working folder when it is verified to match the currently loaded image.
- Added source-image identity metadata for reliable matching, with backward-compatible inference for older Module 3 folders.
- Windows EROFS rebuild preparation now runs in the background worker, preventing the GTK interface from freezing while metadata and the PAX tar stream are generated.
- Preserved all Project_Working_Folders, including empty directories, in release archives.

## v1.2.13
- Windows Module 3 EROFS rebuild no longer invokes host/Cygwin SELinux label lookup.
- Windows EROFS rebuild now creates an erofs-utils-compatible PAX tar stream carrying Android uid/gid/mode, SELinux xattrs, and fs_config capabilities directly.
- This removes the recurring `selabel_lookup` `Error 22` failure on Windows while preserving Android filesystem metadata.
- Linux Module 3 behavior remains unchanged.

## v1.2.12
- Fixed Windows EROFS SELinux lookup failures caused by afsr `security.selinux` xattrs carrying their required trailing NUL byte into generated `file_contexts.txt`. The NUL is now stripped only when converting the xattr into the text-based Android SELinux context file.
- Preserved the original xattr metadata for EXT4/afsr use.
- Preserved all empty `Project_Working_Folders` directories in release archives.

## v1.2.11
- Fixed the remaining Windows EROFS SELinux lookup failure by passing `--mount-point` without a leading slash. erofs-utils constructs the SELinux lookup path as `/<mount-point>/<relative-path>`, so passing `/extracted_system` produced a double-slash path and libselinux returned `EINVAL`.
- Kept Android `file_contexts.txt` entries in image-relative/mount-point form and retained `.repack_info` isolation.
- Preserved all empty `Project_Working_Folders` directories in release archives.

## v1.2.10
- Fixed Windows EROFS SELinux context generation: mkfs.erofs already prepends `--mount-point` to image-relative paths before calling libselinux, so Windows rebuilds now pass the Android/image-relative `file_contexts.txt` unchanged instead of incorrectly prefixing it with the host working-directory path.
- Preserved `.repack_info` isolation and all empty `Project_Working_Folders` directories in release archives.

## v1.2.9
- Fixed Windows EROFS rebuild preparation to read `file_contexts.txt` from the temporary metadata copy after `.repack_info` is moved out of the source tree.
- Preserved the v1.2.8 SELinux path adaptation and `.repack_info` isolation fixes.
- Preserved all empty `Project_Working_Folders` directories in release archives.

## v1.2.8
- Fixed Windows EROFS SELinux context lookup by adapting Android file_contexts patterns to the Cygwin source-tree paths used by mkfs.erofs.
- Preserved the original Android file_contexts metadata for cross-format conversion; only the temporary mkfs input is path-adapted.

## v1.2.7
- Fixed Windows EROFS rebuilds failing on `.repack_info` shared-xattr/SELinux processing by keeping toolkit metadata outside the mkfs source tree during image creation.
- Preserved all Project_Working_Folders directories, including empty folders, in release archives.

## v1.2.4
- Windows Module 3 extraction now preserves a common cross-format metadata set so one working folder can be rebuilt as either EXT4 or EROFS.
- EROFS extraction captures `fs-config.txt` and `file_contexts.txt` from the native extract.erofs backend and derives afsr `fs_metadata.toml` from them.
- EXT4 extraction keeps afsr `fs_metadata.toml` and also generates Android `fs-config.txt` and `file_contexts.txt` for EROFS rebuilds.
- Windows rebuild now validates only the metadata required by the selected output filesystem.
- Module 3 metadata files under `.repack_info` are excluded from generated filesystem images.
- Sparse EXT4 images are converted with the bundled Windows `simg2img.exe` before afsr extraction.
- afsr is downloaded on first Windows EXT4 use and verified against the published SHA-256 release digest.
- Linux Module 3 implementation remains unchanged.

## v1.2.3
- Windows Module 3 EXT4 rebuild now uses the bundled `mke2fs.exe` plus a verified native Windows `e2fsdroid.exe` backend.
- The EXT4 backend preserves the existing `.repack_info/fs-config.txt` and `file_contexts.txt` metadata path used by Linux.
- `e2fsdroid.exe` and its required Cygwin runtime DLLs are fetched on demand and verified against their Git blob SHA-1 values before use.
- Linux Module 3 scripts were not modified.

## v1.2.2
- Module 3 Windows no longer requires WSL.
- Added native userspace EROFS extraction/rebuild using a verified Cygwin x86_64 EROFS tool bundle downloaded on first use.
- Windows EXT4 remains gated until the native Android metadata population backend is added; Linux EXT4 behavior is unchanged.

## v1.2 — 2026-09-12
- Device Tools Theme dropdown now receives the active theme color treatment, including hover/focus states.

## v1.1.7 — 2026-09-12
- Added the themed **Check for Updates** control beside the Device Tools theme selector.
- Added a themed update-available dialog showing the remote version and changelog before installation.
- Added explicit **Continue Update** and **Decline** choices.
- Kept update metadata checks and .7z download/install work off the GTK UI thread.

# v1.1.6 - Integrated Updater — 2026-09-12
- Added a **Check for Updates** button beside the Device Tools theme selector.
- Added background update metadata checks against the A-Team Git repository.
- Added `.7z` update download, optional SHA-256 verification, staged extraction, install, and automatic restart.
- Update installation preserves the existing toolkit working folders and user settings.
- Added support for the `display`, `file`, `changelog`, and optional `sha256` fields in `Updates/version.json`.

## v1.1 — 2026-09-12
- Fixed Linux folder launching so the desktop file manager opens as the invoking desktop user instead of inheriting the toolkit's root privileges.
- Module 3 (EROFS / EXT4 Rebuilder) no longer opens its module working-folder root; it opens the concrete extracted image folder when available.
- Applied the non-root folder opener to the other module folder-opening actions as well.

## v1.0.4 — 2026-09-12
- Unified module output handling with a thread-safe, batched GTK log buffer.
- All module output windows now auto-scroll to the newest output.
- Batched high-volume output updates to reduce UI lag during fast tool output.
- Updated Super Image Creator to use the same buffered live output behavior.

## v1.0.3 — 2026-09-12
- Added A-Team Orange, A-Team Cyan, A-Team Red, and A-Team Pink Device Tools themes.
- Added matching theme-colored Device Tools banners and control styling.

## v1.0.1 — 2026-09-11
- Live Fastboot/ADB device-command progress restored. The status bar now updates as command output arrives while - preserving the existing completion dialog and full captured output.

## v1.0 – 
- 2nd Public Release...

## v0.5.58 — Consolidated Device Tools / Package Cleanup
- Device Tools startup centering corrected and preserved across initial GTK allocation.
- Preserved the working responsive resize and horizontal scrolling behavior.
- Preserved existing Device Tools button/input widths.
- FASTBOOT FLASH image selection displays only the selected image filename while retaining the full path internally.
- FASTBOOT FLASH controls use the established compact layout and fixed 28 px panel gaps.
- Startup sudo password prompt is cleared and hidden after Continue.
- Module 5 Vendor Boot Extract/Rebuild staging and Clean operation were corrected.
- Android Blue is the default Device Tools theme when no saved theme exists, with selected theme persistence.
- Package permissions were normalized and temporary Python cache/test artifacts cleaned from release packages.
- Main window/workspace sizing and snapping fixes were preserved.

## v0.5.57 — 2026-09-11
- Fixed desktop left/right window snapping by removing the hidden 900 px horizontal minimum from the workspace scroller.
- Preserved the existing Modules panel width and workspace expansion behavior.
- Left ADB, FASTBOOT, and FASTBOOT FLASH Device Tools window code unchanged.

## v0.5.56 — 2026-09-10
- Moved the Modules panel flush to the far left of the main workspace area while retaining its 285 px width.
- Removed remaining layout/shadow spacing around the module list.
- Made the Workspace explicitly fill all horizontal space remaining beside the Modules panel.
- Preserved the existing ADB, FASTBOOT, and FASTBOOT FLASH Device Tools code unchanged.

## v0.5.55 — 2026-09-10
- Updated launcher.py to be the authoritative GUI version source (VERSION = "0.5.55").
- Updated the GUI version fallback to follow ATEAM_TOOLKIT_VERSION from the launcher.
- Fixed ateam_settings.ini ownership/permissions after saving from the root-running application.
- Fixed main workspace geometry so the Modules panel is flush left and the Workspace butts directly against it.
- Removed the hard content-width minimum that interfered with desktop side snapping.
- Kept the ADB, FASTBOOT, and FASTBOOT FLASH Device Tools window code unchanged.
- Retained the Vendor Boot Extract / Rebuild browse-parent fix from the current release.

## v0.5.54 — 2026-09-10
- Vendor Boot Extract / Rebuild Run Tool workflow updated.
- Improved module workspace sizing and module work-area expansion.
- Continued Device Tools and window-management fixes.

## v0.5.53 — 2026-09-10
- Adjusted Modules / Workspace layout and module work-area sizing.

## v0.5.52 — 2026-09-10
- Fixed Vendor Boot Extract / Rebuild Run Tool command construction and output handling.

## v0.5.51 — 2026-09-10
- Improved desktop window snapping behavior.
- Swapped Module 6 and Module 7 assignments.

## v0.5.50 — 2026-09-10
- Fixed A-Team settings file locking/ownership.
- Fixed Vendor Boot Extract / Rebuild browse handling.
- Improved native window behavior.

## v0.5.49 — 2026-09-10
- Connected launcher version to the GUI footer.
- Fixed Super Image Creator output ownership.
- Fixed EROFS / EXT4 working-folder handling and ownership.
- Fixed Fastboot Flash worker startup and progress reporting.

## v0.5.48 — 2026-09-10
- Fixed EROFS / EXT4 extraction destination handling.
- Restored user ownership for extracted working folders.

## v0.5.47 — 2026-09-10
- Added automatic terminal scrolling for EROFS / EXT4 operations.

## v0.5.46 — 2026-09-10
- Restored user ownership for Super Image Extractor output folders.

## v0.5.45 — 2026-09-10
- Added root startup and in-memory sudo password handling.
- Improved EROFS / EXT4 selected-folder extraction/rebuild behavior.
- Reworked Android Image Kitchen around the Super Image Creator workflow.
- Improved Device Tools theme selector sizing.
- Added Fastboot Flash progress status.

## v0.5.44 — 2026-09-10
- Expanded module work areas and input/terminal widths.

## v0.5.4
- Reworked Device Tools with A-Team cyber-tech banner themes.
- Added selectable Green, Blue, and Purple Device Tools themes.
- Fixed duplicated ADB/Fastboot executable arguments.
- Kept Sideload ZIP and Logcat controls functional over the artwork.

## v0.02
- Simplified script code.
- Added system_dlkm support.
- Added dual-boot A+B image support.

## v0.01
- Initial release.
