# Screenshot Organizer — repository handoff

Copy these files into the root of the existing Android Studio project. Keep its Gradle wrapper, version catalog, source tree, and manifest. Do not replace the project with a generated template.

Read in this order: `SRS.md` (what the app must do), `DESIGN.md` (how it looks and behaves), `ARCHITECTURE.md` (how it works), `TEST_PLAN.md` (proof), `BUILD_PLAN.md` (implementation order), `TOOLS.md` (recommended tools). Codex reads `AGENTS.md` automatically from the repository root.

Status: product specification, not implementation. Version 1.0, 29 October 2026. Decisions that require codebase inspection or device testing are marked as such. This specification does not imply that the starter project already implements any feature.

Product promise: **Turn screenshots into information you can find, use, and revisit.** The home-screen widgets show meaningful content rather than screenshot counts.

Quick start in Codex: open the entire repository, ensure Gradle sync and a starter emulator run succeed, then use the Stage 0 prompt in `BUILD_PLAN.md`. Require a working build and device evidence at every stage.

The source handbook supplied for design guidance is a Next.js/web design document. Its constraint and visual-audit principles inform `DESIGN.md`; its web-specific fonts, Tailwind, Playwright, SVG noise, 21st.dev components, and JavaScript animation instructions are intentionally not applied to this native Android app.
