# Native Android design specification — Screenshot Organizer

Version 1.0 · Purpose: a restrained, memorable interface that makes saved information legible and usable. Implement in Jetpack Compose and Android widgets. This is a product interface, not a marketing page.

## 1. Design thesis

The screenshot is a source document. The app presents the useful excerpt, the user's interpretation, and the next action. Show the full image when inspection is needed. A saved slide should read like a brief note; a ticket like an upcoming plan; a receipt like a retrievable record. Do not infer high-stakes details without user confirmation.

Unique behavior comes from an **excerpt-first list** and **content-first widgets**, not visual ornament. Reference comparison: Pixel Screenshots offers search and collections; Keepsi has an inbox; other apps offer OCR and category stats. Do not copy their screenshot grid or present generic category dashboards as the main view.

## 2. Native adaptation of the supplied anti-slop handbook

Keep: exclusions of generic visual tropes; stable spacing/type tokens; external design review; visual inspection of working states; useful controls instead of dead mockups.

Do not port: Tailwind, CSS utility strings, Next.js fonts, Playwright browser snapshots, 21st.dev/Origin UI web components, SVG grain overlays, hard-coded desktop Swiss grids, forced spring animations, and tiny 11px uppercase metadata. On Android those increase size, harm legibility, or simply do not render natively. Figma is useful only if actual approved frames exist. Device/emulator screenshots and Compose Preview are the visual feedback loop.

## 3. Explicit exclusions

- No purple/indigo/cyan gradient, frosted glass, neon glow, noise texture, decorative or stock AI artwork, floating 3D screenshot cards, huge empty hero area, oversized rounded pills, card-within-card stacks, or icon-circle feature grids.
- No repeated bold 'AI' labels, fake intelligence claims, placeholder statistics, motivational copy, or anthropomorphic assistant.
- No unverified autofill of reminders, hidden swipe-only action, modal for every save, full-screen onboarding carousel, or permission request at first launch.
- No low-contrast grey metadata or microscopic type to achieve an 'editorial' appearance. No motion that slows a frequent action.
- No hard-coded pixel layout that breaks with font scaling, screen size, RTL, or system navigation.

## 4. Visual system: initial tokens to prototype and validate

Use dp/sp and semantic Compose tokens. Values are starting constraints, subject to accessibility/device inspection; record intentional deviations in this file.

| Role | Light | Dark | Rule |
|---|---|---|---|
| Canvas | `#F7F6F2` | `#171A1D` | Warm, quiet, no texture overlay. |
| Primary text | `#20252A` | `#F0F2F2` | Strong contrast for titles and excerpts. |
| Secondary text | `#59636B` | `#A9B4BC` | Validate WCAG contrast with actual surface. |
| Surface | `#FFFEFA` | `#22272B` | One level above canvas; no diffuse drop shadow. |
| Divider | `#D8DAD6` | `#394147` | 1dp where useful; not every element needs a border. |
| Accent | `#315F5B` | `#91C4BB` | Deep teal for active actions; test contrast. |
| Warning | `#A45535` | `#E3A084` | Only for meaningful error/attention states. |

Typography: use the platform's high-quality sans as the default for compactness and localization; consider a **single licensed, bundled serif** only for a small number of large headings after measuring its size and testing Hindi/RTL fallback. Never mandate three custom font files merely to look different. Initial scale: screen title 28sp/34sp medium; section title 20sp/26sp medium; item title 17sp/23sp semibold; excerpt 15sp/22sp regular; controls 14sp/20sp medium; metadata 12–13sp/18sp regular. Support system font scaling instead of locking these sizes.

Spacing: 4dp base; screen side padding 20dp on a standard phone; list row vertical gap 12dp; component inner padding 12–16dp; section gap 24–32dp. Use visual grouping rather than an empty page. Corners: image preview 6dp, content surface 12dp, bottom sheet 16dp top corners; widget shape follows launcher/system guidance. Border radii are consistent, not a mathematical constraint at the expense of native touch behavior. Touch targets at least 48dp; list content remains comfortable at large fonts.

Icons: use one coherent native vector family with consistent stroke/weight. Labels accompany unfamiliar actions. No random emoji or mismatched icon packs.

## 5. Information hierarchy and screen anatomy

### Today

Top: small product wordmark or simple title, unobtrusive settings affordance. First content: **Next up** only when a reminder exists. Next: **Pinned** items with readable excerpts. Last: **New** screenshots requiring a decision; show a count only when actionable. Empty Today: 'Save something worth keeping' plus Import action and a short share-sheet hint; no empty cards or fake content.

Each Today section has one clear heading and compact 'See all' only if the list exceeds available space. Do not force endless scrolling through all screenshots here.

### Library

Search field visible on entry with hint 'Search text in screenshots and notes'. Under it, list title/excerpt-first rows. A row contains: title, up to two excerpt lines, small source thumbnail, date, and a quiet pin/reminder indicator if relevant. Search results show a highlighted matching snippet and distinguish 'In your note' from 'In screenshot'. Avoid a colorful category chip on every row. Provide sensible date grouping only if it improves scanability.

### Item detail

Header: editable title and source/date. Main area: image preview with tap-to-zoom. Under image: **Your note** (editable) and **Text found in image** (copy/search). Bottom action rail or context-aware buttons: Pin, Remind, Copy, Share, More. Keep actions stable as the keyboard appears. An OCR date is a suggestion, never an automatic reminder. Missing image state preserves text/note and clearly says what happened.

### Save entry point

Share/import opens a lightweight confirmation view with a small preview, editable suggested title and excerpt, and prominent Keep/Pin/Remind choices. User may save immediately without classifying. Multiple imports show progress and individual failure count; never block all items because one failed.

### Settings

Small list: Appearance (system/light/dark), Widgets (per-widget privacy), Reminders (permission status), Recognition (model/status/retry), Storage and privacy, About. No feature flags disguised as user settings.

## 6. Widget specification

**Pinned widget**: one visual system in multiple responsive sizes. Small shows one selected title and one or two excerpt lines. Medium shows one item with readable excerpt or two compact entries. Large shows up to three entries. Tap opens exact item. Configuration lets user choose items and whether text/thumbnail is visible. Default hide details for likely sensitive categories, and allow 'titles only' globally. Handle empty state with a 'Choose an item' deep link.

**Next up widget**: next confirmed reminder with time and title; larger size adds a second. Tap opens item; optional Done/Snooze if reliable in that size. No ticking clock animation, dense calendar, or forced background refresh. On state changes, refresh only affected widgets. Do not render huge bitmaps into launcher memory.

## 7. Motion, accessibility, and copy

Motion: subtle 120–220ms changes for navigation and state transitions when platform setting allows; reduce or remove when animations disabled. Save success can use a short state change and undo affordance. Never animate OCR results in character by character. Respect predictive back and keyboard animation.

Accessibility: contrast check on all token pairs; 48dp targets; clear content descriptions; TalkBack order follows visual hierarchy; images described as screenshot and title, not raw OCR dump; separate accessible actions for Pin/Remind; no color-only status; screen works at 200% font scale and narrow width; never require swipes without visible alternative. Test long Hindi and English examples; handle RTL if localized.

Copy: concrete verbs ('Pin', 'Set reminder', 'Copy text', 'Remove from app'). Distinguish 'Remove from app' from 'Delete screenshot'. Use plain states: 'Reading text', 'Text recognition needs a one-time download', 'Original image unavailable'. No 'magic', 'supercharge', 'your second brain', or invented AI status.

## 8. Review gate before implementation is called polished

Create Compose previews with realistic fixtures: lecture slide, noisy receipt, timetable, empty/failed OCR, long title, missing source, and permission denial. Capture actual device screens for Today, Library, Detail, search, save, both widgets, and settings in light/dark, small/large font, and narrow phone. Compare hierarchy, line wrapping, thumbnail crop, tap size, widget readability, visual density, and contrast. Fix the observed issues and log changes. Curated Figma frames may be used as source of truth if the user approves them; a generic generated Figma design is not automatically authoritative.
