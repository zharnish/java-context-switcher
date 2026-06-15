# Spec Requirements Document

> Spec: Polish and npm Distribution
> Created: 2026-06-15
> Status: Planning

## Overview

Package the Java Context Switcher as a production-ready npm module that users can launch with a single `npx java-context-switcher` command, with no manual build steps. This phase covers bundling the frontend into the backend, wiring the npm `bin` entry point, integrating Heroicons into the UI, adding robust error handling and path validation, and publishing a polished README.

## User Stories

### Zero-Install Launch via npx

As a Java developer, I want to run `npx java-context-switcher` in any terminal and have the app open in my browser automatically, so that I can start switching Java versions without cloning a repo, running build commands, or configuring anything.

The user runs the command, the server starts, the browser opens to `localhost:3001`, and the app is immediately usable. The first run may take a few seconds for `npx` to download the package; subsequent runs use the cached version.

### Resilient Error Handling

As a Java developer, I want the app to display clear error messages when something goes wrong (e.g. an invalid path or a failed switch), so that I can understand the problem and fix it without reading logs.

When the user adds a version with a path that doesn't exist, the form shows an inline error immediately. When a switch operation fails (e.g. the JDK directory was moved), a descriptive error toast explains what went wrong. The app never silently fails.

## Spec Scope

1. **npx Entry Point** - Add a `bin` entry to `package.json` that executes the compiled backend server, enabling `npx java-context-switcher` launch.
2. **Frontend Bundle in Backend** - Vite builds the React frontend into `backend/dist/public/`; the Express server serves these static files so the app is self-contained in one process.
3. **Auto-Open Browser** - On server start, the default browser opens to `http://localhost:[PORT]` automatically.
4. **Heroicons Integration** - Replace placeholder text controls (add, delete, status indicator) with Heroicons SVG icons from `@heroicons/react`.
5. **Path Existence Validation** - Frontend-side check warns the user before submitting if the entered path looks invalid (basic length/format check); definitive server-side check returns a clear error message.
6. **Graceful Error Handling** - All API error responses surface human-readable messages in the UI; unhandled exceptions in the backend are caught and logged without crashing the server.
7. **README** - Complete `README.md` at the repo root with usage instructions, feature overview, configuration reference, screenshots placeholder, and contribution guide.

## Out of Scope

- Changes to the core version switching logic or config file format (Phase 1 scope)
- Multiple environment variable targets (Phase 3)
- Import/export config via UI (Phase 3)
- macOS/Linux support (Phase 3)
- Team/shared config features (Phase 3)

## Expected Deliverable

1. Running `npx java-context-switcher` (after local `npm link` for testing) starts the server and opens the browser to `localhost:3001` without any additional steps.
2. The app is served entirely from the Express backend — no separate Vite dev server is needed in production mode.
3. Adding a version entry with a non-existent path shows a clear error; clicking switch on a broken path shows a descriptive toast error, not a generic "500" message.
4. All Heroicons are visible and correctly sized in the UI (add button, delete controls, active version indicator).
5. `README.md` documents all installation, usage, and configuration steps accurately.
