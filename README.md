# puzzles

Puzzles is an offline puzzle collection: Sudoku (classic and killer), Klondike Solitaire, Two Tone, Sightlines, Five Letters, Kakuro, Slide Out and Word Wheel. It ships as an Android app and as a website.

This repository is a wrapper. It holds the three project repositories as git submodules, each pinned to the latest commit on its `main`.

| Folder | Repository | What it is |
| --- | --- | --- |
| `puzzles-android/` | [joclegg/puzzles-android](https://github.com/joclegg/puzzles-android) | The Android app: Kotlin, Jetpack Compose, Room. Every merge to `main` publishes a release with a signed APK and app bundle. It also holds the puzzle generators. |
| `puzzles-web/` | [joclegg/puzzles-web](https://github.com/joclegg/puzzles-web) | The website: Vite, React, TypeScript, Tailwind. Every merge to `main` deploys to https://joclegg.github.io/puzzles-web/. |
| `puzzles-data/` | [joclegg/puzzles-data](https://github.com/joclegg/puzzles-data) | The puzzle banks and word lists both apps use. Both apps include it as their own `puzzles-data/` submodule. |

## Getting it

```sh
git clone --recurse-submodules https://github.com/joclegg/puzzles.git
```

Or, in an existing clone: `git submodule update --init --recursive`.

## Keeping it current

`.github/workflows/update-submodules.yml` moves every submodule to its latest `main` and commits the change. It runs:

- when one of the three repositories merges to `main` (each sends a `submodule-merged` event),
- by hand, from the Actions tab.

The three repositories send the event with a `PUZZLES_DISPATCH_TOKEN` secret: a fine-grained personal access token with **Contents: read and write** on `joclegg/puzzles`.
