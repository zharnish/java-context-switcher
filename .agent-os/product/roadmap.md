# Product Roadmap

## Phase 1: Core MVP

**Goal:** Deliver a working localhost web app where users can add Java version buttons and click them to switch their environment variable.
**Success Criteria:** A user can run `npx java-context-switcher`, open `localhost:3001`, add a Java version, click it, and verify `JAVA_HOME` has been updated in their Windows environment.

### Features

- [ ] Initialize Node.js + Express + TypeScript backend project `S`
- [ ] Initialize React + TypeScript + Vite frontend project `S`
- [ ] `GET /api/versions` — return all saved Java version entries from config file `S`
- [ ] `POST /api/versions` — add a new Java version entry to config file `S`
- [ ] `DELETE /api/versions/:id` — remove a Java version entry from config file `S`
- [ ] `POST /api/switch` — execute batch script to set the environment variable to the selected JDK path `M`
- [ ] Config file read/write service (Markdown-formatted `java-versions.md`) `S`
- [ ] Java version button list UI (React + Bootstrap) `S`
- [ ] Add Java Version form UI (name + path inputs) `S`
- [ ] Delete button per version entry `XS`
- [ ] Active version visual indicator (highlighted button) `XS`

### Dependencies

- Node.js 18+ installed on developer machine
- Windows OS (batch script execution)
- npm / npx available globally

---

## Phase 2: Setup & Guidance

**Goal:** Reduce onboarding friction by embedding download links and setup instructions so new users can go from zero to a working config without leaving the app.
**Success Criteria:** A developer with no prior `JAVA_HOME` configuration can follow in-app instructions to download, install, and configure a JDK version without external documentation.

### Features

- [ ] Environment variable name settings panel (configurable, defaults to `JAVA_HOME`) `S`
- [ ] Oracle Java downloads link surfaced prominently in the UI `XS`
- [ ] In-app setup instructions panel: download, install, locate JDK path steps `S`
- [ ] Port configuration support (reads `PORT` env var, default `3001`) `XS`
- [ ] Settings persisted to config file (env var name, port preference) `S`
- [ ] Toast/alert feedback when version switch succeeds or fails `S`

### Dependencies

- Phase 1 complete
- Config file schema extended to include settings section

---

## Phase 3: Polish & Distribution

**Goal:** Make the app production-ready for npm publication and improve the overall UX quality.
**Success Criteria:** The package is published to npm and users can run it via `npx java-context-switcher` with no additional setup.

### Features

- [ ] Package project for `npx` distribution (bin entry, package.json setup) `M`
- [ ] Bundle frontend into backend server static assets `M`
- [ ] Auto-open browser on server start `XS`
- [ ] Graceful error handling for invalid paths, missing JDK directory, script failures `S`
- [ ] Path validation when adding a Java version (verify directory exists) `S`
- [ ] README with usage instructions, screenshots, and contribution guide `S`
- [ ] Heroicons integrated into button actions (add, delete, switch indicator) `XS`

### Dependencies

- Phase 2 complete
- npm publish access to `zharnish` account

---

## Phase 4: Advanced Configuration

**Goal:** Support power users who need more control over their environment and configuration.
**Success Criteria:** Users can override the config file location, export/import their config, annotate versions with notes, and reorder their version buttons.

### Features

- [ ] Config file path override (store `java-versions.md` in a user-specified location) `S`
- [ ] Import/export config file via UI (upload/download the Markdown file) `M`
- [ ] Version notes field — users can attach notes to each Java version entry `S`
- [ ] Drag-to-reorder version buttons `M`

### Dependencies

- Phase 3 complete

---

## Phase 5: Enterprise & Team Features

**Goal:** Enable teams to share a base configuration and reduce per-developer setup time.
**Success Criteria:** A team lead can generate a shareable config and new developers can import it in under 2 minutes.

### Features

- [ ] Shareable config URL or file export for team distribution `L`
- [ ] Read-only "team defaults" config layer merged with personal config `L`
- [ ] Audit log — record which version was switched and when `M`
- [ ] macOS/Linux support (shell script execution in addition to batch) `XL`
- [ ] System tray integration via Electron wrapper (optional) `XL`

### Dependencies

- Phase 4 complete
