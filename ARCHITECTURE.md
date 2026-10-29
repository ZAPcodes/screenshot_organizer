# Android implementation architecture

Version 1.0 · Implement only after inspecting the generated project's actual Gradle and Android versions.

## 1. Stack and dependency policy

- Kotlin, Jetpack Compose, Material 3 as a behavior/accessibility foundation, Navigation Compose if needed. Customize tokens and components according to `DESIGN.md`.
- Room for item, note, pin, reminder, import, and OCR metadata; Room FTS virtual table or verified equivalent for local full-text search. DataStore for small settings.
- Android photo picker and Sharesheet input. Use platform `MediaStore` only for explicitly opt-in auto-discovery. Avoid `MANAGE_EXTERNAL_STORAGE` and accessibility capture.
- ML Kit on-device Text Recognition v2; prefer Google Play services unbundled model for size. Do not assume the model is installed. Add a script only when justified by target users; test Hindi/Latin mix if Devanagari is supported.
- WorkManager for deferrable import/OCR/index work; run small user-triggered operations promptly, keep processing idempotent. Use Glance/RemoteViews for two widgets according to current support. Choose reminder scheduler based on observed timing needs; do not request exact-alarm privilege by default.
- Thumbnail loader may use a maintained Android image library only after inspecting dependency weight and URI handling. Never load full image at list/widget size.
- Use build system's version catalog and current compatible stable dependency versions; pin reproducible versions. No arbitrary AI SDK, backend, or network dependency.

## 2. Data model (conceptual)

`ScreenshotItem`: id, source URI or private-copy reference, import source, importedAt, sourceDate if known, original availability, OCR state/error, OCR text, OCR version, user title, user excerpt, user note, keptAt, inbox state, pinnedAt, sensitive visibility flag. Store actual schema and migration strategy in code.

`Reminder`: id, itemId, label, dueInstant, chosenZone, status (scheduled/done/snoozed/cancelled), notification permission state at last attempt. One item may have multiple reminders only if deliberately supported; V1 UI can limit to one active reminder.

`Collection` and item-collection join: collection ID/name/order and associated item IDs. Removing a collection only removes associations, never the underlying item or original image.

`WidgetConfig`: appWidgetId, widget type, selected item IDs or next-up rule, privacy mode, configured size/state. Widget IDs can disappear; clean stale configurations on removal.

`SearchIndex`: normalized title, excerpt, note, OCR text with item ID. Prefer transactional updates and FTS ranking; a re-index operation must not overwrite user fields.

Define clear lifecycle states: `NEW → KEPT → DISMISSED`; pin and reminder are attributes on kept items. OCR is orthogonal: `PENDING → READY | NO_TEXT | MODEL_UNAVAILABLE | FAILED`. Missing original is orthogonal and does not delete text.

## 3. Data flow

1. Photo picker/share intent yields content URI(s). Validate type, count, size, and readability. Persist URI grant when supported; otherwise copy selected input to app-private storage if durability requires it. Do not copy all gallery images.
2. Insert an idempotent item record. Show it immediately with `PENDING` state. Queue OCR and thumbnail work off the main thread.
3. OCR produces text; update index transactionally. Preserve any user edits. If model unavailable, expose retry and schedule only an appropriate retry.
4. Search queries local index; snippet generation identifies match origin. Detail reads original lazily. Source access failure surfaces an explicit state.
5. Pin/reminder mutations persist first, then update relevant widgets/notification schedule. Widgets render bounded text and tiny safe thumbnails only where enabled.
6. On reboot/process restoration, reconcile scheduled reminders and widget configurations from database. Never rely only on in-memory state.

## 4. Package boundaries (suggested, not mandatory)

`data/` Room, repository, migrations; `import/` URI handling, share entry, picker; `ocr/` recognizer and work; `search/` FTS; `reminders/` scheduler and receiver; `widgets/` config, receivers, rendering; `ui/` screens, components, theme, ViewModels. A small project can consolidate related files, but keep platform I/O out of composables.

Single-activity Compose navigation is appropriate. Use typed route arguments with stable IDs, not image bytes. Preview fixtures are confined to debug/previews/test; production screen loads repositories.

## 5. Permissions and platform constraints

- Photo picker and share flow require no blanket gallery permission. Persist selected URI access where the system allows it and test across reboot. For share URIs without durable grants, copy just the accepted image to private storage with an explained storage policy.
- Media reading and change observation for optional auto-discovery must comply with Android version-specific partial access and Google Play's broad-media declaration. A content change trigger is an opportunity to reconcile, not an instant-capture guarantee.
- Request Android 13+ notification permission when user sets a reminder, with a functional in-app fallback if denied.
- For deletion of media owned by another app, Android requires user-facing consent. V1's dismiss operation only changes the local index and own private copy.
- Widget receivers are brief. Delegate expensive work; Android widget updates have scheduling/battery constraints. Deep links must validate IDs and safe content.
- Prefer normal, user-visible reminder scheduling. Document limitations of OS battery policy and vendor behavior. Avoid promising precise seconds.

## 6. Failure containment and migrations

All operations return explicit success/failure states. A bad image must not halt batch import. Work may rerun without duplicate records or duplicate reminders. Worker cancellation must leave record recoverable. Keep schema migrations explicit and tested before shipping updates. Database backup/export is a post-V1 decision; don't claim backup exists.

## 7. Source-of-truth implementation references

Before coding an API, consult the current official Android documentation for Photo Picker, receive data from other apps, MediaStore permissions, WorkManager, app widgets/Glance, notifications, Room, Compose tests, and Play policy. The exact API and dependency versions in this document are intentionally not frozen to a machine-specific starter template.
