# Repository instructions for Codex

This repository is a native Android screenshot organizer. Read `SRS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `TEST_PLAN.md`, and `BUILD_PLAN.md` before changing application code. If a document conflicts with actual platform behavior, use current official Android guidance, record the deviation, and update the affected specification.

## Operating rules

- Inspect the existing Gradle files, version catalog, manifest, source tree, minSdk, targetSdk, and installed tools first. Preserve the existing project and Kotlin/Compose build conventions.
- Work one vertical slice from `BUILD_PLAN.md` at a time. Each slice must build, run on an emulator or connected phone, and demonstrate a real interaction. Never pass sample data off as imported content.
- Keep UI state separate from persistence and platform APIs. Do not put import, OCR, or database logic into composables. Avoid premature modules or generic abstraction layers.
- Build for offline, local use. No account, analytics, cloud, ads, API key, broad file-management permission, accessibility service, or persistent foreground service unless the user explicitly changes the scope.
- Match `DESIGN.md`. Do not add web styles or components, decorative AI imagery, marketing copy, statistics dashboards, arbitrary gradients, or novelty animation. No dead buttons.
- Every screen needs loading, empty, error, long-text, and accessibility states. No silent failure. Respect system back, edge-to-edge insets, dynamic text, TalkBack, dark mode, and reduced motion.
- Every feature needs a verification path. Add targeted tests for parsing, persistence, search, state transitions, and critical Compose flows. Inspect actual screenshots on at least two sizes before claiming visual completion.
- After each slice report: changed files, user-visible behavior, exact build/test/device checks, screenshot observations, unresolved limitations. Distinguish 'not tested' from 'passed'.
- Never claim bug-free or production-ready solely because compilation succeeds. Do not commit credentials, signing keys, personal screenshots, `local.properties`, `.gradle`, or build outputs.

## Done for a slice

`assembleDebug` succeeds; relevant tests pass; the app starts and completes the slice's interaction on a device; error and empty states are exercised; visual output is inspected; the feature checklist in `BUILD_PLAN.md` is updated. If no device is available, stop at build/test verification and clearly report the device gap.
