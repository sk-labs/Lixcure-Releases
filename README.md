# Lixcure-Releases

Public release artifacts for the Lixcure HMS desktop app. This repo holds
**built app files only** — never source code, patient data, or credentials.
The HMS app downloads its updates from here, anonymously, so nothing secret
may ever be committed to this repo.

## Layout

- `version.json` — the update manifest the app checks. Latest version,
  release notes, and one download URL + SHA-256 hash per OS.
- `builds/hms-<os>-v<version>.zip` — one release zip per OS. Since v1.0.2
  the zip uses the launcher layout:
  - `lixcure_hms` — the launcher binary (compiled from
    `apps/hms_desktop/tool/launcher.dart` in the source repo). The hospital
    shortcut points here, not at the app binary.
  - `versions/<version>/` — the app bundle for that version (binary,
    `data/`, `lib/`).
  - The launcher starts `current_version`, and if that version exits with
    an error within 10 seconds of starting, it automatically starts the
    previous installed version instead, records the failed version in
    `bad_versions`, and leaves `fallback_notice.txt` so the app can tell
    the user on its next start.

## Cutting a release

1. Build the app for the target OS, e.g.
   `flutter build linux --release --dart-define=APP_VERSION=1.2.3`
   (the version baked in must match the release version).
2. Compile the launcher:
   `dart compile exe tool/launcher.dart -o lixcure_hms`
3. Assemble the zip from the install root:
   `lixcure_hms` (launcher) + `versions/1.2.3/` (bundle contents).
4. Hash it: `sha256sum hms-linux-v1.2.3.zip`.
5. Upload the zip to `builds/` here and update `version.json`:
   `latest_version`, `notes`, and the OS asset's `url` + `sha256`.
6. Hospital computers pick the update up on their next check (automatic
   every 3 hours while the app runs, or manual from About > Check for
   updates). The app verifies the hash before installing, installs
   side-by-side, and restarts through the launcher — so a version that
   crashes on launch falls back immediately instead of on the next
   manual start.

## Releases

- v1.0.3 (Linux) — launcher fallback fixes: pointer recorded at start,
  crashed versions excluded from About revert targets.
- v1.0.2 (Linux) — launcher fallback release. v1.0.1 was a test-only
  release (flat bundle, no launcher) and was superseded before any
  hospital install; its zip was removed.
- v1.0.1 (Linux, retired) — first auto-update release: 3-hour + manual
  update checks, verified downloads, side-by-side install, About sidebar
  section.
