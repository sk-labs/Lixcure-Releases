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
   `flutter build windows --release --dart-define=APP_VERSION=1.2.3`
   (the version baked in must match the release version).
2. Compile the launcher (per-OS binary: `lixcure_hms` on Linux,
   `lixcure_hms.exe` on Windows):
   `dart compile exe tool/launcher.dart -o lixcure_hms`
3. Assemble the zip from the install root:
   `lixcure_hms(.exe)` (launcher) + `versions/1.2.3/` (bundle contents).
4. Hash it: `sha256sum hms-<os>-v1.2.3.zip`.
5. Upload the zip to `builds/` here and update `version.json`:
   `latest_version`, `notes`, and the OS asset's `url` + `sha256`.
   NOTE: `latest_version` is global, so every release should ideally carry
   an asset per OS the field installs run on. A manifest without this
   release's asset for some OS makes those installs report a
   "No build for <os>" error at their next check — visible, harmless, and
   the installed version keeps running; it never installs a wrong or
   stale zip across platforms.
6. Hospital computers pick the update up on their next check (automatic
   every 3 hours while the app runs, or manual from About > Check for
   updates). The app verifies the hash before installing, installs
   side-by-side, and restarts through the launcher — so a version that
   crashes on launch falls back immediately instead of on the next
   manual start.

## Releases

- v1.2.0 (Windows) — Accounts & Other staff form: HR creates sign-in
  accounts for every role except admin (receptionist with the OPD/ER/GYN
  counter desk picker, pharmacist, accountant, front office, HR, support
  staff); admin minting is server-guarded to Central Command only.
- v1.1.0 (Windows) — first server-verified license release: activation
  binds the device, first-run gating (activation form / subscription
  screen / app), bootstrap Admin + HR accounts issued with the license,
  hub issuing + renew + revoke wizard. Linux stays on v1.0.3.
- v1.0.3 (Linux) — launcher fallback fixes: pointer recorded at start,
  crashed versions excluded from About revert targets.
- v1.0.2 (Linux) — launcher fallback release. v1.0.1 was a test-only
  release (flat bundle, no launcher) and was superseded before any
  hospital install; its zip was removed.
- v1.0.1 (Linux, retired) — first auto-update release: 3-hour + manual
  update checks, verified downloads, side-by-side install, About sidebar
  section.
