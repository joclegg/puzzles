# puzzles

Puzzles is an offline puzzle collection: Sudoku (classic and killer), Klondike Solitaire, Two Tone, Sightlines, Five Letters, Kakuro, Slide Out and Word Wheel. It ships as an Android app and as a website.

This repository is a wrapper. It holds the five project repositories as git submodules, each pinned to the latest commit on its `main`.

| Folder | Repository | What it is |
| --- | --- | --- |
| `puzzles-android/` | [joclegg/puzzles-android](https://github.com/joclegg/puzzles-android) | The Android app: Kotlin, Jetpack Compose, Room. Every merge to `main` publishes a release with a signed APK and app bundle. It also holds the puzzle generators. |
| `puzzles-web/` | [joclegg/puzzles-web](https://github.com/joclegg/puzzles-web) | The website: Vite, React, TypeScript, Tailwind. Every merge to `main` deploys to https://joclegg.github.io/puzzles-web/. |
| `puzzles-data/` | [joclegg/puzzles-data](https://github.com/joclegg/puzzles-data) | The puzzle banks and word lists both apps use. Both apps include it as their own `puzzles-data/` submodule. |
| `puzzles-backend/` | [joclegg/puzzles-backend](https://github.com/joclegg/puzzles-backend) | The Supabase backend that syncs scores and history between both apps for signed-in players. |
| `joclegg-infra/` | [joclegg/joclegg-infra](https://github.com/joclegg/joclegg-infra) | The Terraform for everything the joclegg account runs on: HCP Terraform, every GitHub repository and their Actions secrets. Every merge to `main` applies it. |

## Getting it

```sh
git clone --recurse-submodules https://github.com/joclegg/puzzles.git
```

Or, in an existing clone: `git submodule update --init --recursive`.

## Keeping it current

`.github/workflows/update-submodules.yml` moves every submodule to its latest `main` and commits the change. It runs:

- when one of the five repositories merges to `main` (each sends a `submodule-merged` event),
- by hand, from the Actions tab.

The five repositories send the event with a 1-hour token from the `joclegg-puzzles-dispatch` GitHub App, which is installed only on `joclegg/puzzles` with Contents write. Each has the app's key as the `PUZZLES_DISPATCH_APP_KEY` secret and its Client ID as the `PUZZLES_DISPATCH_APP_CLIENT_ID` variable, both set by [joclegg-infra](https://github.com/joclegg/joclegg-infra).
