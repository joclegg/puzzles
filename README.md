# puzzles

Puzzles is a collection of eight offline puzzle games: Sudoku (classic and killer), Klondike Solitaire, Two Tone, Sightlines, Five Letters, Kakuro, Slide Out and Word Wheel. It ships as an Android app, an iOS app and a website, which all play the same, and syncs saved games between them for players who sign in.

This repository holds no code of its own. It gathers the six Puzzles repositories as git submodules, each pinned to the latest commit on its `main`, so the whole project can be cloned, browsed and searched in one place.

## The repositories

| Folder | Repository | What it is |
| --- | --- | --- |
| `puzzles-android/` | [joclegg/puzzles-android](https://github.com/joclegg/puzzles-android) | The Android app, in Kotlin with Jetpack Compose, Room and DataStore. It also holds the puzzle generators. Every merge to `main` publishes a release with a signed APK and app bundle. |
| `puzzles-ios/` | [joclegg/puzzles-ios](https://github.com/joclegg/puzzles-ios) | The iOS app, in Swift with SwiftUI and SwiftData. Every merge to `main` publishes a release with a simulator build. |
| `puzzles-web/` | [joclegg/puzzles-web](https://github.com/joclegg/puzzles-web) | The website, in TypeScript with React, Vite and Tailwind. Every merge to `main` deploys to https://puzzles.joclegg.dev. |
| `puzzles-data/` | [joclegg/puzzles-data](https://github.com/joclegg/puzzles-data) | The puzzle banks and word lists. Every merge to `main` publishes them to https://joclegg.github.io/puzzles-data/, where all three apps download them, so new puzzles need no app release. |
| `puzzles-backend/` | [joclegg/puzzles-backend](https://github.com/joclegg/puzzles-backend) | The Supabase database schema that syncs saved games and settings for signed-in players. Every merge to `main` pushes its migrations. |
| `joclegg-infra/` | [joclegg/joclegg-infra](https://github.com/joclegg/joclegg-infra) | The Terraform for everything the apps run on: the GitHub repositories, the `joclegg.dev` domain and its Workers, sign-in, email and the Supabase project. Every merge to `main` applies it. |

Each repository has its own `README.md` and `AGENTS.md`, with how to build, test and change it.

## Getting it

```sh
git clone --recurse-submodules https://github.com/joclegg/puzzles.git
```

In an existing clone, fetch the submodules with `git submodule update --init --recursive`.

## Making changes

Change a project in its own repository, through a pull request to its `main`. Work inside the submodule folder as in any clone:

```sh
cd puzzles-web
git switch -c my-change origin/main
# edit, commit, push, open a pull request in joclegg/puzzles-web
```

Nothing is committed here by hand: once the pull request merges, this repository moves its submodule on its own.

## Keeping it current

`.github/workflows/update-submodules.yml` moves every submodule to its latest `main` and merges the change through a pull request. It runs when any of the six repositories merges to `main`, and by hand from the Actions tab.

Each of the six has a push webhook, set in joclegg-infra, to a Cloudflare Worker. When `main` moves, the Worker sends this repository a `submodule-merged` event, using a short-lived token from the `joclegg-repo-dispatch` GitHub App.

To catch up a local clone with the latest of every repository:

```sh
git pull
git submodule update --init --recursive
```
