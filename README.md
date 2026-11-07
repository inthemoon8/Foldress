[![Download](https://img.shields.io/badge/-DOWNLOAD-2378c7?style=for-the-badge)](https://github.com/inthemoon8/Foldress/releases/latest/download/FoldressSetup.exe)


# Foldress

Give Windows folders recognizable icons directly from File Explorer's right-click menu.

Foldress can find a likely main executable inside a folder and use its embedded icon, or let you choose a file and apply its icon to one of its parent folders. There is no separate dashboard to launch for everyday use.

## Features

- **Automatic folder icons:** select one or more folders and choose **Automatically set folder icons**. Each folder is processed separately.
- **Manual parent selection:** right-click a single file, open **Apply this icon to a parent folder**, and choose a numbered parent path. `1` means the file's containing folder.
- **Multiple icon sources:** EXE embedded icons, ICO files, and PNG, JPG/JPEG, BMP, GIF, and TIF/TIFF images. Other files use the icon provided by Windows Explorer; this does not extract arbitrary document contents as images.
- **Restore previous settings:** a **Restore folder icon** menu restores the saved folder-icon configuration.
- **Cursor feedback:** loading stays below the pointer until processing finishes. The result shows the target path or an error and fades over about three seconds. A successful final result can also show an icon preview. This is result feedback, not a separate preview-and-confirm editor.
- **Slow-search choice:** when search progress passes roughly two seconds, a dialog offers continuing, choosing a file manually, or skipping that folder. This is a progress-based check, not a guaranteed two-second timeout.
- **Permission retry:** an access-denied operation can request Windows administrator approval and retry that operation. Approval does not guarantee access to every protected folder.
- **Explorer refresh requests:** after applying or restoring an icon, Foldress notifies Explorer to refresh the affected folder and its containing view. Display timing can still depend on Explorer's cache.

## Requirements

- Windows with File Explorer. The current installer is built for **64-bit Windows**.
- The .NET Framework 4.x runtime and Windows Installer. The installer does not bundle a .NET runtime installer.
- Permission to install the Explorer integration and write to the folders you customize. The default installation location is under Program Files.

There is no published compatibility matrix covering every Windows or .NET Framework version. Windows on ARM and 32-bit Windows have not been verified for this installer.

## Installation

1. Obtain the Foldress setup EXE supplied with the release.
2. Run it and choose the installation location. Use **Browse…** to choose a drive or folder instead of typing a path.
3. Complete the Windows Installer prompts, including administrator approval if requested.

The default destination is `C:\Program Files\Foldress`. Choosing `D:\` creates `D:\Foldress`; choosing a folder already named `Foldress` avoids adding another `Foldress` beneath it.

The setup EXE contains its MSI package and extracts it temporarily. A separate MSI download is not required. Current build scripts name the setup file `FoldressSetup-2.0.0-preview5.exe`; a distributed copy may be renamed `FoldressSetup.exe` without changing its contents.

Keep the installed worker, DLL, and icon together in their installation folder. Moving the installation folder manually can break registered paths. This is an installed Explorer extension, not a portable application.

If setup fails, its error message can include a temporary diagnostic-log path. Successful or cancelled setup attempts clean up that log when possible.

## Usage

### Automatically choose an icon

1. Select one or more application folders in File Explorer.
2. Right-click and choose **Automatically set folder icons**.
3. Wait for the result below the pointer. Respond to a slow-search prompt if one appears.

Automatic selection is a heuristic. It favors executables near the folder root, names matching the folder, and matching product descriptions. Names associated with installers, uninstallers, updaters, and helper tools receive a lower score. It can select the wrong executable or find no suitable icon.

Search is bounded: it considers executables through four subfolder levels, visits at most approximately 1,500 directories, and collects at most 5,000 candidates. Linked locations are skipped and some inaccessible subfolders may be skipped. Continuing a slow search does not remove these limits.

### Choose the source yourself

1. Find an EXE, ICO, supported image, or another file whose Explorer icon you want to use.
2. Right-click that **single file**.
3. Open **Apply this icon to a parent folder**.
4. Click the desired numbered folder path.

For an EXE, Foldress reads embedded icon resources. For supported images, it generates a multi-size ICO. For other files, it requests the file's Explorer icon. Not every file will provide a useful or distinctive icon.

You can replace an automatically selected icon by applying a different file's icon manually. The file must be inside the target folder's directory tree. The manual picker offered during a slow search enforces the same restriction.

Mixed file-and-folder selections and multiple-file manual selections do not get these menus. On Windows versions that separate classic context menus, you may need to look under **Show more options**; menu placement has not been verified across all Explorer versions.

### Restore a folder

Right-click the customized folder and choose **Restore folder icon**. Multiple folders can be selected.

Restore uses Foldress's saved baseline; it is not a history of every icon change. If another tool has modified `desktop.ini` since Foldress last applied an icon, Foldress can refuse restoration to avoid overwriting those changes. Preserve the backup and inspect the conflicting file rather than deleting the backup to force a restore.

## Files saved in customized folders

Foldress uses Windows' `desktop.ini` folder customization mechanism. It stores the icon and restore information inside the target folder:

```text
Your folder/
├── desktop.ini
└── .folder-icon-studio/
    ├── icon-v2.ico
    └── state.xml
```

The storage directory is hidden. `desktop.ini` receives hidden/system attributes, and the folder receives the read-only attribute used for Explorer customization. Existing settings and relevant original attributes are backed up for restoration.

The icon reference is relative to the folder, reducing dependence on the original EXE or image location. Keep these generated files with the folder when moving it. Deleting the icon file can break its appearance; deleting `state.xml` removes the saved information needed for restoration.

The `.folder-icon-studio` name is retained by the current implementation despite the Foldress product name.

## Language

The setup launcher, runtime menus, and cursor notices select Korean for a Korean Windows UI language and English otherwise. File names and paths remain unchanged.

The MSI uses the language supplied by the setup launcher. When launched directly for maintenance or removal without that value, it can fall back to the stored installation language, then the Windows Installer user-language setting. Changing Windows' display language after installation therefore does not guarantee that every removal dialog changes language immediately. Windows-owned dialogs may also follow Windows' own localization rules.

## Uninstalling

Remove **Foldress** through Windows Settings' installed-apps list or Control Panel's Programs and Features. Windows Installer manages removal of the installed files and the registered Explorer menus.

Already customized folders keep their icons and local restore data. Restore those folders **before uninstalling** if you want to return them to their previous appearance.

The worker exits after its work and result notice finish. However, the menu DLL loads inside Explorer and can remain in use after the worker exits. The installer supports Windows Restart Manager: **Close apps and continue** lets Windows close applications holding the files and restart applications that support restarting. Explorer windows may close. Finish file transfers and save your work first.

If automatic closing is unavailable or fails, a file-in-use prompt or Windows restart may still be necessary. Removal is not guaranteed to be prompt-free on every system. An empty installation folder or user-added files may remain; removal does not recursively wipe arbitrary contents of the installation directory.

Do not manually delete Windows Installer's cache under `C:\Windows\Installer`.

## Limitations and verification

- Source review and local checks covered icon extraction and conversion, color/alpha preservation, restore behavior, menu construction, path handling, language conditions, and installer configuration. Production worker processes were also checked for automatic exit after applying and restoring icons.
- During development, the project owner reported successful installation, icon use, removal of the Explorer menus, and removal after using the extension. In the latest reported removal test, an empty installation folder remained. These are observations from that user's environment, not a guarantee for other machines.
- Full installation/removal was not independently automated across Windows versions. The Restart Manager configuration was checked, but app shutdown and restart behavior was not independently exercised by the development automation.
- Microsoft Defender custom file scans of the current development setup EXE, MSI, worker, and DLL reported **no threats** on November 7, 2026. This is a result for those specific build files, not certification, a guarantee against future detections, or a comprehensive runtime-behavior test. Renaming a file does not add a digital signature or security certification.
- Symbolic links, junctions, and other reparse-point paths are rejected or skipped. Search limits and permissions can prevent an automatic match. Windows folder-icon behavior on network shares, cloud-synced folders, removable media, and unusual file systems has not been comprehensively verified.
- Operation logs are attempted in the installation folder's `logs` directory. If that location is not writable, logging can fail without preventing the cursor result from appearing.
- There is no separate application dashboard, candidate-list editor, or guaranteed pre-application preview workflow in the current release.

## Source and build notes

The current release build is defined by `work/foldress-msi/build.ps1`. Other folders under `work/` contain earlier implementations, fixtures, and development artifacts; their presence does not mean their features are included in the current installer. In particular, older custom uninstallers and CMD installation scripts are not the current installation method.

| Current source | Responsibility |
| --- | --- |
| `work/foldress-msi/Setup.cs` | Setup launcher, UI-language selection, temporary MSI extraction, setup diagnostics |
| `work/foldress-msi/BuildMsi.cs` | Windows Installer package, folder browser, registration, removal dialogs |
| `work/foldress-msi/src/AncestorMenu.cs` | Explorer file and multi-folder context menus |
| `work/foldress-msi/src/WorkerMsi.cs` | Short-lived worker entry point |
| `work/foldress-msi/src/Core.cs` | EXE ranking, icon extraction, `desktop.ini`, backups, restoration, refresh requests |
| `work/foldress-msi/src/FileSources.cs` | Image conversion and Explorer-provided file icons |
| `work/foldress-msi/src/CursorNotice.cs`, `CursorSurface.cs` | Cursor-following feedback and fading |
| `work/foldress-msi/src/SearchConsent.cs`, `Elevation.cs` | Slow-search choices and permission retries |
| `work/foldress-msi/src/Locale.cs`, `DeploymentMsi.cs` | Runtime translations and installation-path lookup |

Build from the repository's current directory layout on Windows:

```powershell
& .\work\foldress-msi\build.ps1
```

The script uses the .NET Framework C# compiler at `%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\csc.exe`, Windows `makecab`, and the icon at `work/foldress-msi/payload/Foldress.ico`. It produces the setup EXE and MSI under `outputs/`.

Development checks are available in `test.ps1`, `verify.ps1`, and `verify-completion.ps1` under `work/foldress-msi/`. The regression-test script also uses test sources under `work/foldress-images/dev/`; preserve that dependency if reorganizing the repository. Configuration checks do not perform a real install or uninstall and do not substitute for testing on a clean Windows system.
