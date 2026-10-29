# Codex build plan and prompts

Version 1.0 · Use one stage per coding turn. Commit a working checkpoint after each stage. Do not proceed past an unverified dependency.

## Stage 0 — Inspect and baseline

Inspect project, Gradle/version catalog, manifest, Kotlin/Compose setup, min/target SDK, Git status, Android SDK, emulator/adb. Run Gradle sync or `gradlew.bat :app:assembleDebug` on Windows; launch starter app. Report exact configuration and any blocked tooling. Add the repository docs without overwriting existing project files. Fix only baseline issues. Select permanent application ID with user before Play publication, not by guessing a domain.

**Prompt:** 'Read AGENTS.md and all specification files. Inspect this existing Android Studio project, report the configuration and baseline build/device result, and plan Stage 1 against actual files. Preserve the project and do not implement later stages.'

## Stage 1 — Native visual foundation

Build Today, Library, Detail, and Settings using clearly marked preview fixtures and a reusable token system. Build realistic empty, long, error, and dark states. Create Compose Preview configurations. Run on emulator; inspect screenshots for normal/large font and two widths. Implement navigation and back behavior. No fake persistence or dead buttons in release UI; preview fixtures remain debug-only.

**Gate:** review real screenshots and interaction hierarchy; user can navigate without broken controls. Update `DESIGN.md` with intentional deviations.

## Stage 2 — Import and persistence

Implement Photo Picker, sharesheet for one/multiple images, Room records, durable URI/private-copy strategy, duplicate import handling, source-loss state. Replace preview list with real items. Test force stop/reboot and malformed inputs. No OCR prerequisite yet; text can be manually entered.

**Gate:** select/share a real image, edit/save it, restart app, reopen original.

## Stage 3 — OCR and search

Integrate on-device recognition with explicit model states; index OCR/title/excerpt/note and show result snippets. Keep user edits separate from recognition. Test on text, no-text, failed, and multilingual fixtures as scoped.

**Gate:** find a real imported screenshot by an interior word, offline after model installed.

## Stage 4 — Actions and reminders

Complete keep, pin, note, dismiss (local-only), collections, reminder scheduling, notification permission flow, deep links, snooze/done/cancel. Verify time/date, reboot, denial, and state transitions.

**Gate:** full import → edit → pin → reminder → open → dismiss journey, original gallery image intact.

## Stage 5 — Widgets

Build Pinned and Next up widgets with configuration, privacy modes, bounded text, resize behavior, source-loss handling, deep links, and state-triggered updates. Test several launchers if possible. Do not use full-resolution widget bitmaps.

**Gate:** add widgets, resize, change data, force stop/reboot; no stale or sensitive text leak.

## Stage 6 — Product hardening

Run `TEST_PLAN.md`; measure package/storage/start/search/scroll; audit permissions, exported activities, accessibility, localization readiness, Play policy, and release build. Fix failures, document limits. Then evaluate optional auto-discovery as V1.1; it must not destabilize manual import.

**Gate:** release checklist passed with recorded evidence; remaining limitations explicit.

## Standard instruction to append to every stage prompt

'Implement only this stage. Build and run it on the available Android emulator or device; use Compose previews and device screenshots to inspect visual output; fix observed defects; run targeted tests. Report changed files, exact commands and outcomes, screenshots inspected, what remains unverified, and whether the stage gate passed. Do not describe mock UI as a complete product.'
