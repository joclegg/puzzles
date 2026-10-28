# Agent notes for puzzles

This is a wrapper repository. It holds no code of its own: only the four project repositories as git submodules, and a workflow that keeps them current. See `README.md` for the project overview.

## Projects

- `puzzles-android/`: the Android app ([joclegg/puzzles-android](https://github.com/joclegg/puzzles-android)). Kotlin, Jetpack Compose, Room and DataStore, a module per game, Maestro e2e flows. It also holds the puzzle generators (`./gradlew :<game>:engine:generateBank`). Read its `AGENTS.md` before changing it.
- `puzzles-web/`: the website ([joclegg/puzzles-web](https://github.com/joclegg/puzzles-web)). Vite, React 19, TypeScript, Tailwind, Vitest and Playwright. Read its `AGENTS.md` before changing it.
- `puzzles-data/`: the shared puzzle banks and word lists ([joclegg/puzzles-data](https://github.com/joclegg/puzzles-data)), one puzzle per line. Both apps include it as their own `puzzles-data/` submodule. Its `README.md` documents every file format.
- `puzzles-backend/`: the Supabase backend ([joclegg/puzzles-backend](https://github.com/joclegg/puzzles-backend)). SQL migrations under `supabase/migrations/`, shared by both apps for score and history sync. Read its `README.md` before changing it.

Both apps have the same eight games and should behave the same. A change to one usually needs the same change in the other.

## Rules

- MUST NOT commit project changes here. Change a project in its own repository, through a pull request, and let the workflow move the submodule.
- MUST change puzzle banks in `puzzles-data` first, then bump the `puzzles-data` submodule in both apps.
- You don't need to move the submodules yourself: when a project merges to `main`, `.github/workflows/update-submodules.yml` moves every submodule to the latest `main` and commits it here automatically. MUST NOT bump them by hand, except to fix that workflow.
- Each project repository sends a `submodule-merged` `repository_dispatch` event here after a merge to `main`, using the `PUZZLES_DISPATCH_TOKEN` secret.
