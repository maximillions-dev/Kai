# AGENTS.md

## Project structure

3 Gradle modules: `:composeApp` (shared UI + desktop), `:androidApp` (Android shell), `:screenshotTests` (Paparazzi).
Kotlin Multiplatform + Compose Multiplatform. Java 21, Gradle 9.5.1.

## Key commands

| Action | Command |
|---|---|
| Format code | `./gradlew spotlessApply --no-daemon` |
| Check formatting (CI fails on this) | `./gradlew spotlessCheck --no-daemon` |
| Desktop tests (CI runs these) | `./gradlew :composeApp:desktopTest --no-daemon` |
| Record + copy screenshots | `./gradlew :screenshotTests:updateScreenshots --no-daemon` |
| Verify screenshots vs golden | `./gradlew :screenshotTests:verifyPaparazziDebug --no-daemon` |
| Quick screenshot record (kai-ui only) | `./gradlew :screenshotTests:recordKaiUiScreenshots --no-daemon` |
| Generate Play Store screenshots | `./gradlew :screenshotTests:generateStoreScreenshots` |
| Convert site PNGs → WebP | `./scripts/convert-site-images-to-webp.sh` |

## Spotless quirks

Several ktlint rules disabled in root `build.gradle.kts`:
`no-wildcard-imports`, `package-name`, `function-naming`, `discouraged-comment-location`, `value-argument-comment`, `value-parameter-comment`.

## Testing conventions

- **Framework:** `kotlin.test` (`@Test`, `@BeforeTest`, `@AfterTest`). No Mockito/MockK — hand-written fakes only (e.g. `FakeDataRepository`).
- **Flow testing:** `app.cash.turbine` (`viewModel.state.test { awaitItem() ... }`).
- **Coroutines:** `@BeforeTest` sets `Dispatchers.setMain(StandardTestDispatcher())`, `@AfterTest` resets.
- **Screenshot tests:** Use JUnit 4 `@Rule` with Paparazzi (hybrid). Golden images at `screenshotTests/src/test/snapshots/images/` (gitignored). CI auto-commits updated screenshots.
- CI runs **only desktop tests** — no Android instrumentation or WASM tests.

## Screenshot pipelines

Two pipelines in the README:
1. **README/site screenshots:** `updateScreenshots` — records Paparazzi goldens, copies to `screenshots/` and `site/img/`. Runs automatically on push.
2. **Store screenshots:** `generateStoreScreenshots` — localized Play Store screenshots. Upload via `bundle exec fastlane android upload_screenshots`.

## Composite action

`.github/actions/setup/action.yml` — shared setup (JDK 21, Gradle cache, optional Android SDK). Used by all workflows.

## CLAUDE.md

Defines rules for updating feature docs in `docs/features/`. Must update "Last verified" date and Key Files table when modifying feature logic.
