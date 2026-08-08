# Build Instructions — GameMod Platform

## Requirements

- Android Studio Koala (2024.1) or newer
- JDK 17 (bundled with recent Android Studio)
- Android SDK Platform 34, Build-Tools 34+
- A device or emulator running Android 8.0 (API 26) or newer

## Option A — Open in Android Studio (recommended)

1. `File → Open`, select the `GameModPlatform/` project root.
2. Let Android Studio sync Gradle (it will generate the wrapper jar automatically the
   first time — an internet connection is needed for this one-time dependency download).
3. Select the `app` run configuration and hit **Run** ▶ on a connected device or emulator.

## Option B — Command line

```bash
cd GameModPlatform

# First time only: if gradle-wrapper.jar is missing, generate it with a local
# Gradle install (8.7+):
gradle wrapper --gradle-version 8.7

# Build a debug APK
./gradlew assembleDebug

# Install directly onto a connected device/emulator
./gradlew installDebug
```

The debug APK is written to `app/build/outputs/apk/debug/app-debug.apk`.

## Project layout

```
GameModPlatform/
├── app/
│   └── src/main/java/com/gamemod/platform/
│       ├── MainActivity.kt              # single-activity Compose entry point
│       ├── GameModApplication.kt        # composition root (manual DI)
│       ├── model/                       # plain data classes (Game, Preset, Feature, ...)
│       ├── adapters/                    # GameAdapter interface, registry, demo adapter
│       │   └── demo/DemoGameAdapter.kt  # sample adapter — safe to test against
│       ├── engine/                      # CustomizationEngine, BackupManager, ValidationEngine
│       ├── data/
│       │   ├── local/                   # Room database, entities, DAOs
│       │   └── repository/              # GameRepository, PresetRepository
│       └── ui/                          # Compose screens, components, navigation, ViewModels
├── docs/
│   ├── BUILD_INSTRUCTIONS.md            # this file
│   └── ADDING_NEW_ADAPTER.md            # how to add support for another game
└── build.gradle.kts / settings.gradle.kts
```

## First run / testing without a real game

The app ships with `DemoGameAdapter`, a fully functional sample that reads/writes a local
JSON file (`demo_config.json`) inside the app's private storage. It exercises every part of
the platform — library listing, live editor, validation, automatic backup, restore, and
presets — without touching any real commercial game. Launch the app, open **My Games**, tap
**Sandbox Quest (Demo)**, and start editing.

## Permissions

The app requests only `INTERNET` and `ACCESS_NETWORK_STATE`, used solely for optionally
fetching official game metadata or mod listings. All configuration editing, presets, and
backups work fully offline in the app's private, sandboxed storage — no storage permission
is required on API 26+.

## Notes on production readiness

This is a functional framework and reference implementation. Before shipping:
- Replace the manual DI in `ViewModelFactory`/`GameModApplication` with Hilt if the team
  prefers a DI framework as the adapter count grows.
- Add real artwork for game icons (currently emoji placeholders) and a proper launcher icon.
- Add unit tests for each new `GameAdapter.validate()` implementation (see
  `docs/ADDING_NEW_ADAPTER.md`).
- Review each new adapter against the safety checklist before merging (also in
  `docs/ADDING_NEW_ADAPTER.md`).
