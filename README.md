# Lixcure-Releases

Public release artifacts for the Lixcure HMS desktop app. This repo holds
**built app files only** — never source code, patient data, or credentials.
The HMS app downloads its updates from here, anonymously, so nothing secret
may ever be committed to this repo.

## Layout

- `version.json` — the update manifest the app checks. Latest version,
  release notes, and one download URL + SHA-256 hash per OS.
- `builds/hms-<os>-v<version>.zip` — the app bundle for one OS, zipped
  from inside the bundle directory (so the binary, `data/` and `lib/`
  sit at the zip root).

## Cutting a release

1. Build the app for the target OS, e.g.
   `flutter build linux --release --dart-define=APP_VERSION=1.2.3`
   (the version baked in must match the release version).
2. Zip the bundle contents:
   `cd build/linux/x64/release/bundle && zip -r hms-linux-v1.2.3.zip .`
3. Hash it: `sha256sum hms-linux-v1.2.3.zip`.
4. Upload the zip to `builds/` here and update `version.json`:
   `latest_version`, `notes`, and the OS asset's `url` + `sha256`.
5. Hospital computers pick the update up on their next check (automatic
   every 3 hours while the app runs, or manual from About > Check for
   updates). The app verifies the hash before installing and keeps the
   previous version for rollback from About.

## Current release

- v1.0.1 (Linux) — first auto-update release: 3-hour + manual update
  checks, verified downloads, side-by-side install, About sidebar section.
