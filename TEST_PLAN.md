# Verification plan and release checklist

Version 1.0 · Tests should prove behavior and catch regressions. Do not write trivial tests that merely mirror implementation details.

## Environments

Use the project's lowest supported API level and a current Android version; one narrow phone emulator, one common-size emulator, and one midrange physical phone when available. Test one Google Play services-enabled emulator for unbundled OCR. Test light/dark, font scale 1.0/2.0, portrait/landscape where relevant, offline after model installation, and TalkBack. Keep fixture images synthetic or redacted.

## Fixture pack

Create local test fixtures: text-heavy English lecture slide; English/Hindi mixed slide if language support selected; payment receipt with sensitive data replaced by dummy values; train/flight ticket with date; dense timetable; chat with long text; screenshot with no text; near-duplicate images; malformed image; huge image; item whose URI later disappears. Never commit real OTP, ID, bank, medical, or chat data.

## Automated checks

| Layer | Test |
|---|---|
| JVM/Room | Import identity/idempotency, keep/dismiss transitions, user edits survive OCR retry, index updates and ranking, migration path. |
| OCR integration | Model ready/unavailable/no-text/failed states; reprocessing leaves user note untouched. |
| Compose UI | Empty Today, save/keep, edit note, search result and snippet, permission denial, missing source; semantic labels and accessible alternative to gestures. |
| Android integration | Sharesheet single/multiple, URI grant persistence/copy fallback, notification deep link and reschedule, widget add/update/remove/resize/reboot. |
| Visual | Approved Compose Preview screenshot references for key screens and both widget sizes; review failures intentionally. |

## Manual acceptance matrix

| Case | Steps | Expected |
|---|---|---|
| First launch | Fresh install, decline every optional permission | Useful empty state; manual import available. |
| Import/restart | Select 3 images, force stop, relaunch, reboot | All accepted images or explicit missing-source state; no duplicates. |
| Share | Share 1 and then 3 images from gallery | Each opens/imports; partial failure reported individually. |
| OCR | Import text and no-text images; temporarily withhold model/network | Correct status; retry works; offline search works after model installed. |
| User editing | Edit title/excerpt/note, reprocess OCR | Edits unchanged and searchable. |
| Search | Query image word, note word, nonsense string, long query | Correct result/snippet, empty state, no main-thread stutter. |
| Reminder | Schedule, deny notifications, grant later, snooze, complete, reboot | In-app state accurate; alerts conditional on permission; no duplicate firing. |
| Widgets | Add both, resize, pin/unpin, edit item, kill app, reboot | Correct data, useful empty state, no launcher crash or stale private text. |
| Privacy | Pin dummy sensitive screenshot and use titles-only setting | No unapproved text/thumb on widget or logs. |
| Source loss | Remove original source or revoke media access | Note/OCR retained; helpful recovery state; no crash. |
| Dismiss | Dismiss item with gallery open | Gallery original remains; app search and widget update. |
| Accessibility | TalkBack and 200% font on narrow screen | Every action labeled and reachable; no clipping/hidden required action. |

## Performance and quality evidence

Record actual installed package size; app-private storage for 100 and 1000 items; cold start on target device; time to first item after import; OCR processing time for typical/large screenshots; search latency; scroll smoothness; background drain over a normal day. Set concrete thresholds after baseline, optimize observed regressions, and include device/OS details with numbers. Do not fabricate metrics. Inspect Android vitals/crash data if a release cohort exists.

## Definition of done

No crash or silent data loss in listed high-risk flows; critical tests green; release build created; design review performed on real screenshots; exported components and permissions reviewed; privacy description and Play declarations match implementation; known device-specific limitations documented in README. Compilation alone is not completion.
