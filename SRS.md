# Software Requirements Specification — Screenshot Organizer

Version 1.0 · 29 October 2026 · Native Android · Status: implementation specification

## 1. Purpose and scope

The app helps a person capture or import screenshots, understand why they saved them, retrieve their content, attach their own notes, and surface important items on the home screen. The first release is local-first, account-free, and useful without a backend. The app complements the device gallery; it must clearly distinguish removing an item from this app from deleting the original image.

### Product principles

1. Show the useful information before the screenshot thumbnail, while preserving access to the original image.
2. A screenshot can be kept without categorizing it. Pin, note, and remind are optional one-step actions.
3. The user controls titles, excerpts, notes, reminders, and widget visibility. OCR text is evidence, not infallible truth.
4. Access and permissions are requested in context. No upload is necessary for core functionality.
5. Honest status: importing, waiting for OCR model, indexing, permission denied, missing image, reminder unavailable.

### Primary users and jobs

- Student: turn a PDF or lecture-slide screenshot into a searchable, editable note; pin a timetable or formula.
- Busy saver: find a booking, address, order ID, or receipt by words or date.
- Planner: link a reminder to a screenshot and see the next item on a widget.

### Terminology

- **Item:** one screenshot record with stable internal ID, origin URI/reference, OCR state, user data, and timestamps.
- **Keep:** retain item in the app's library. It does not move or duplicate the source image by default.
- **Dismiss:** remove item from the app inbox/library after confirmation or undo; does not delete the original.
- **Pin:** mark item for the Pinned widget. Pinning alone never exposes its full text until widget privacy configuration allows it.
- **Reminder:** user-confirmed time and short label linked to an item. OCR date suggestions never schedule automatically.

## 2. Release scope

### V1 required

Photo picker import; Android share-to-app for one or multiple images; persistent local records; on-device OCR; editable title/excerpt/notes; full-text search over OCR and user fields; Today/Library/Detail screens; Keep/Pin/Remind/Dismiss actions; optional user-created collections; Pinned and Next up widgets; local notification for confirmed reminders; settings for widget privacy and removal behavior; robust state and permission handling; dark mode and accessibility.

### V1.1 candidate after validation

Opt-in automatic discovery of new screenshot images with platform-appropriate media permission and background reconciliation; optional Devanagari OCR; duplicate suggestions; export/backup. These are not silent V1 dependencies. The import and share flow must remain fully usable if automatic discovery is unavailable.

### Explicitly out of V1

LLM chat, generated study summaries, automatic financial extraction, global screen capture, tracking source app, cloud sync, accounts, social feed, spending dashboard, automatic deletion of original media, background polling service, and an in-app image editor.

## 3. Functional requirements

Each `FR` must have a real implementation and an acceptance check; a sample-data screen is insufficient.

| ID | Requirement | Acceptance |
|---|---|---|
| FR-01 | On first launch, explain the product in one concise screen and offer Import and Share guidance. The user can skip permission grants. | User reaches an empty Today screen without granting media or notification access. |
| FR-02 | Import one or multiple `image/*` items using Android's photo picker. | Imported image persists after force stop/restart; duplicate selection does not create duplicate records for the same source. |
| FR-03 | Receive single and multiple images from Android Sharesheet, validate actual MIME/content, and process off the main thread. | Share a screenshot from Photos/files; item opens and is searchable after OCR; malformed/oversize/unreadable input shows a clear error. |
| FR-04 | Keep an item linked to its original URI, with persisted access where available; safely handle revoked access, moved/deleted original, and permission loss. | After reboot, retained image opens; if unavailable, note/OCR remains and UI offers recovery/removal without crash. |
| FR-05 | Run on-device OCR after import; show pending, ready, unavailable, and failed states; permit retry. | Text-rich sample is indexed; model download wait and no-text image have distinct messages. No raw OCR is silently presented as user-authored notes. |
| FR-06 | Store user-editable title, short excerpt, and freeform note independently of OCR. | Edits survive process death and remain after OCR reprocessing. A failed OCR run does not erase edits. |
| FR-07 | Search title, excerpt, note, and OCR text locally; rank user fields above OCR-only matches; allow a clear no-results state. | Query a word inside an image and a user note; both appear with a relevant snippet and open the correct item. Empty query restores library. |
| FR-08 | Show a Today screen with next confirmed reminders, pinned items, and a small new-items queue. | New user sees helpful empty states; a pinned item and upcoming reminder appear without requiring a category. |
| FR-09 | Show Library with search and a readable title/excerpt-first list, sort by saved date, and a way to inspect original images. | Library handles many entries smoothly; long text truncates predictably without hiding the full content in Detail. |
| FR-10 | Show Detail with original image, zoom, extracted text, editable user content, source status, and actions: copy text, keep, pin/unpin, remind, share image, dismiss. | Each visible control completes its stated operation; no placeholder action. |
| FR-11 | Let user set, edit, complete, snooze, and cancel reminders. Date detected by OCR may prefill a proposal but user must confirm exact time and timezone. | A reminder fires at intended local time within platform scheduling constraints, opens its item, and stays consistent with Today and widget after completion. Denied notification access is explained. |
| FR-12 | Pinned widget shows one to three user-selected items, short editable text, and deep links; supports resizing and per-widget privacy setting. | Add and resize widget; pin/edit/unpin updates it; a missing source image does not crash the launcher. |
| FR-13 | Next up widget shows up to two upcoming reminders, their time and title, and opens correct item. Any completion/snooze action must persist and refresh. | Change reminder and verify widget state, including after process death/reboot. |
| FR-14 | Dismiss removes app record with undo or confirmation; removing original image is a separate, explicit action if ever implemented. | Dismiss does not alter gallery media. Item is no longer in search/widget and associated reminder is cancelled. |
| FR-15 | Settings expose theme (system/light/dark), widget content visibility, notification status, OCR status/retry, and concise storage/privacy explanation. | Changing each setting immediately affects relevant UI; no setting is merely decorative. |
| FR-16 | Optional auto-discovery, when implemented, is opt-in, restricted to screenshots, idempotent, and reconciles missed changes on reopening. | Permission denial leaves manual import usable. Background delay is described honestly; no claim of guaranteed instant detection. |
| FR-17 | Create, rename, and remove user collections; assign/remove an item without making categorization mandatory. | Item can be saved with no collection; filters and search work together; removing a collection does not delete screenshots. |

## 4. Behavioral rules and edge cases

- An imported item initially belongs to the new-items queue. Keeping it clears the queue indicator; pinning or setting a reminder implicitly keeps it.
- A screenshot with no OCR text remains usable as an image with manually entered title/note.
- OCR can produce wrong words or dates. Never auto-schedule, auto-send, auto-delete, or replace user-authored fields based solely on OCR.
- Duplicates: key repeat import by stable source URI plus available media identity; content-hash duplicate suggestions can wait until V1.1. Do not merge separate screenshots without consent.
- If the source image disappears, retain the user's text and reminder metadata until the user chooses otherwise; show a missing-source state.
- Shared URI access can be temporary. Import must secure durable access where allowed or copy only that selected image to app-private storage if needed. Document storage cost and cleanup behavior.
- On-device OCR library can require a one-time model download. Explicitly show progress/unavailable state; never silently return empty results when the model is missing.
- Timezones and daylight changes: store an absolute due instant and the user's chosen timezone/display context; reschedule or reconcile pending reminders after reboot/time changes as platform rules allow.
- Notifications may be denied. Reminder remains visible in app/widget with an explanation that alerts are off.
- Widgets may be seen by anyone looking at an unlocked home screen. Default widget text should be conservative, with a per-widget reveal choice and no OTP/password/ID extraction on home screen by default.
- Search must not log queries or recognized screenshot text to analytics or ordinary debug logs.

## 5. Non-functional requirements

| ID | Requirement | Verification target |
|---|---|---|
| NFR-01 | Privacy: core flow stays on-device; no account, ads, analytics, or upload. | Inspect manifest, dependencies, network calls, and data safety declaration before release. |
| NFR-02 | Lean install and runtime: use existing OS facilities and unbundled OCR where practical; avoid large image copies and persistent workers. | Record release APK/AAB size, storage use with 100/1000 screenshots, cold start, and background battery behavior; compare against baseline. Targets set after first measurement. |
| NFR-03 | Responsive UI: scrolling and typing must remain fluid while OCR or thumbnails process. | Profile on a midrange physical device or representative emulator; no decoding full-resolution images on main thread. |
| NFR-04 | Accessibility: screen reader labels, meaningful reading order, scalable text, sufficient contrast, at least 48dp touch targets, no gesture-only operation. | Test TalkBack, font scaling, light/dark, and small screen. |
| NFR-05 | Reliability: no data loss on process death, reboot, model unavailability, revoked URI, or failed worker; operations idempotent. | Run `TEST_PLAN.md` fault and lifecycle cases. |
| NFR-06 | Security: exported share entry points validate input; use explicit PendingIntent and least permissions; protect private thumbnails/notes. | Review manifest and exported components; no secrets in repository or logs. |
| NFR-07 | Compatibility: choose minSdk/targetSdk from existing project and current Play requirements after inspection. | Run on lowest supported version and a current Android version. Document vendor-specific behavior. |
| NFR-08 | Offline behavior: previously indexed items, search, notes, and widgets work without network. | Airplane mode test after OCR model is installed. |

## 6. Success criteria and release gate

### First-use user journey

Install → import/share a real screenshot → see readable text or clear OCR status → edit title/excerpt → pin it → add widget → find item by a word in image → set reminder → open item from notification/widget → dismiss without deleting original.

The release candidate is acceptable only when this journey works on two Android versions, on at least one physical device and one emulator if available; all visible controls work; high-risk permission/data-loss/widget cases in `TEST_PLAN.md` pass; current design screenshots have been reviewed; Play policy and privacy copy match actual implementation. Measure app size, storage, startup, and search latency. Do not invent a 'zero bugs' claim or performance number before measurement.

## 7. Open implementation decisions

- Permanent application ID and user-facing name before Play release.
- Exact minSdk and targetSdk after inspecting starter Gradle project and current Play rules.
- URI persistence versus app-private copy fallback per import path and Android version.
- Exact reminder scheduler choice, including whether precise alarms are warranted. Prefer inexact scheduling unless user need justifies exact alarms.
- Widget launcher variations and device-specific testing matrix.
- Hindi/Devanagari OCR in V1 or V1.1 based on target-user research and model size/quality tests.
