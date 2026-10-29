# Tooling and plugin decisions

Version 1.0 · 29 October 2026. Install on the Windows machine that has the project, SDK, and emulator. No MCP server is necessary for the core app or its on-device OCR.

## Recommended

1. Android Studio SDK, Gradle wrapper, Device Manager/emulator, `adb`, and Git. Give local Codex access to the whole repository, not one file. Use the existing Gradle wrapper for builds.
2. Google's official Android CLI and Codex plugin: `codex plugin marketplace add android/android-cli-plugin` then `codex plugin add android-cli@android-cli-plugin`. On Windows install Android CLI through `winget install --id Google.AndroidCLI` or the official download. `android init` installs its agent skill when appropriate. Google's Android CLI `android emulator` subcommand is currently disabled on Windows; start devices in Android Studio and use `adb` instead. Official: https://developer.android.com/tools/agents/android-cli/agent-plugins and https://developer.android.com/tools/agents/android-cli/download
3. Official Android `testing-setup` skill, if Android CLI is installed: `android skills add testing-setup`. It helps build Compose tests. Official: https://developer.android.com/develop/ui/compose/testing

## Optional when a real design file exists

Figma's **remote MCP** can bring approved Figma frames, variables, dimensions, and components into Codex. Install the Figma plugin in the Codex app, or for Codex CLI run `codex mcp add figma --url https://mcp.figma.com/mcp` and sign in. Provide links to exact approved frames. Figma access by itself does not supply taste or make unreviewed AI-generated frames authoritative. Official: https://help.figma.com/hc/en-us/articles/39888629089175-Codex-and-Figma-Set-up-the-MCP-server

## Not recommended for this native project

- Playwright MCP tests websites/browser views, not the installed Android Compose UI. Use emulator/device captures, Compose Previews, Compose UI tests, and optional official Compose Preview Screenshot Testing instead.
- 21st.dev Magic, Origin UI, shadcn, Tailwind, Framer Motion, and web-browser component registries output web code. Do not import them into Android Studio.
- Firebase, Supabase, or OpenAI API are unnecessary for V1. Adding them increases size, complexity, and privacy obligations without helping the offline core.
- GitHub integration is optional for remote work, backup, PR review, and CI; ordinary Git and local Codex suffice for development.

## Visual feedback loop

Run Compose Preview and install on a real emulator. Capture Today, Library, Detail, both widgets, and error/empty states in light/dark and large font; compare with `DESIGN.md`; fix actual rendering issues. Add screenshot regression tests for stable key surfaces once design is approved. Official: https://developer.android.com/training/testing/ui-tests/screenshot
