# Agent notes for puzzles

This is a wrapper repository. It holds no code of its own: only the six Puzzles repositories as git submodules, and a workflow that keeps them current. See `README.md` for the overview.

## Repositories

| Folder | Repository | What it is |
| --- | --- | --- |
| `puzzles-android/` | [joclegg/puzzles-android](https://github.com/joclegg/puzzles-android) | The Android app: Kotlin, Jetpack Compose, Room and DataStore. Also the puzzle generators (`./gradlew :<game>:engine:generateBank`). |
| `puzzles-ios/` | [joclegg/puzzles-ios](https://github.com/joclegg/puzzles-ios) | The iOS app: SwiftUI, SwiftData and Swift packages. |
| `puzzles-web/` | [joclegg/puzzles-web](https://github.com/joclegg/puzzles-web) | The website: Vite, React, TypeScript and Tailwind. |
| `puzzles-data/` | [joclegg/puzzles-data](https://github.com/joclegg/puzzles-data) | The puzzle banks and word lists, which every app downloads at runtime. |
| `puzzles-backend/` | [joclegg/puzzles-backend](https://github.com/joclegg/puzzles-backend) | The Supabase schema for score and history sync. |
| `joclegg-infra/` | [joclegg/joclegg-infra](https://github.com/joclegg/joclegg-infra) | The Terraform for everything the apps run on. |

Each repository's own `AGENTS.md` gives its structure and conventions: read it before changing that repository.

## Managing the submodules

- MUST NOT commit project changes here. Change a project in its own repository, through a pull request to its `main`.
- MUST NOT move the submodules by hand, except to fix `.github/workflows/update-submodules.yml`. Each merge to a project's `main` triggers that workflow, which moves every submodule and merges the change.
- MUST make a change that affects how the games play in all three apps: Android, iOS and web have the same eight games and behave the same.
- MUST change puzzle banks in `puzzles-data` first: the apps pick up the published banks on their own.
- MUST add a new submodule to `.gitmodules` with `branch = main`, and to its `submodules` list in joclegg-infra's `github/repositories.tf`, so it gets the webhook that triggers the update.
