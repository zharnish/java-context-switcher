# Spec Requirements Document

> Spec: Core MVP and Onboarding Guidance
> Created: 2026-06-15
> Status: Planning

## Overview

Build the complete working localhost web application for Java Context Switcher — including the Express + TypeScript backend, React + Bootstrap frontend, file-based config persistence, batch-script-driven environment variable switching, and embedded onboarding guidance. This phase delivers a fully functional app where a developer can run `npx java-context-switcher`, configure their Java versions, and switch between them with a single click.

## User Stories

### Switching Java Versions

As a Java developer, I want to click a button labeled with a Java version name, so that my `JAVA_HOME` environment variable is immediately updated to that version's installation path without opening System Properties.

The user opens the app at `localhost:3001`, sees a list of their configured Java versions as clickable buttons, clicks one, and receives confirmation that the environment variable has been set. The currently active version is visually highlighted. Opening a new terminal will reflect the change.

### Managing Java Version Entries

As a Java developer, I want to add and remove named Java version entries, so that I can maintain an up-to-date list of the JDKs installed on my machine.

The user clicks `+ Add Java Version`, enters a display name (e.g. "Java 21 LTS") and the local path to the JDK installation directory, and submits the form. A new button appears immediately. The user can delete any entry with a delete control on each button. All entries survive app restarts via the local config file.

### Getting Started Without Prior Configuration

As a developer new to managing `JAVA_HOME`, I want in-app instructions and a link to download Java, so that I can set up my environment without searching external documentation.

The app surfaces a direct link to the Oracle Java downloads page and displays step-by-step instructions for downloading an installer, running it, locating the resulting installation directory, and entering it into the Add Java Version form. The environment variable name input defaults to `JAVA_HOME` but can be changed for non-standard setups.

## Spec Scope

1. **Express + TypeScript Backend** - Node.js/Express API server with TypeScript, serving REST endpoints for version CRUD and switch operations.
2. **React + TypeScript Frontend** - Vite-based React 18 app using Bootstrap 5 and React-Bootstrap for all UI components.
3. **Config File Service** - Read/write service for a Markdown-formatted `java-versions.md` file that persists version entries and settings.
4. **REST API Endpoints** - `GET /api/versions`, `POST /api/versions`, `DELETE /api/versions/:id`, `POST /api/switch`, `GET /api/settings`, `PUT /api/settings`.
5. **Java Version Button UI** - List of named version buttons with active-state highlighting and per-entry delete controls.
6. **Add Java Version Form** - Modal or inline form with name and path inputs; path pre-validated before saving.
7. **Batch Script Execution** - Backend generates and executes a Windows `setx` batch script when a version switch is requested.
8. **Environment Variable Settings Panel** - UI control to view and update the target environment variable name (defaults to `JAVA_HOME`); persisted to config file.
9. **Oracle Download Link + Setup Instructions** - Prominent link to `https://www.oracle.com/java/technologies/downloads/` and a collapsible step-by-step setup guide embedded in the UI.
10. **Port Configuration** - Server reads `PORT` environment variable, defaults to `3001`.
11. **Switch Feedback** - Toast/alert notifications for successful and failed version switch operations.

## Out of Scope

- npx packaging and npm distribution (Phase 2)
- Bundling frontend assets into the backend (Phase 2)
- Auto-opening browser on server start (Phase 2)
- macOS/Linux shell script support (Phase 3)
- Multiple simultaneous environment variable targets (Phase 3)
- Import/export of config file via UI (Phase 3)
- Team/shared config features (Phase 3)
- Drag-to-reorder version buttons (Phase 3)

## Expected Deliverable

1. Running `node dist/server.js` (or `ts-node src/server.ts`) starts the Express server on port 3001; opening `localhost:3001` loads the React UI.
2. A user can add a Java version entry, see it appear as a button, click it, open a new terminal and run `java -version` (after restarting their shell) to confirm the version changed.
3. All configured version entries persist across server restarts, stored in `java-versions.md` in the user's config directory.
4. The environment variable name defaults to `JAVA_HOME` but can be overridden via the settings panel, with the new name persisted to the config file.
