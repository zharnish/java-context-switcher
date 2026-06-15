# Technical Specification

This is the technical specification for the spec detailed in .agent-os/specs/2026-06-15-core-mvp-and-guidance/spec.md

---

## Technical Requirements

### Project Structure

```
java-context-switcher/
├── backend/
│   ├── src/
│   │   ├── server.ts           # Express app entry point
│   │   ├── routes/
│   │   │   ├── versions.ts     # CRUD routes for version entries
│   │   │   ├── switch.ts       # POST /api/switch route
│   │   │   └── settings.ts     # GET/PUT /api/settings routes
│   │   ├── services/
│   │   │   ├── configFile.ts   # Read/write java-versions.md
│   │   │   └── batchScript.ts  # Generate and execute setx batch script
│   │   └── types/
│   │       └── index.ts        # Shared TypeScript interfaces
│   ├── tsconfig.json
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── main.tsx
│   │   ├── App.tsx
│   │   ├── components/
│   │   │   ├── VersionButton.tsx      # Individual Java version button
│   │   │   ├── VersionList.tsx        # Grid/list of version buttons
│   │   │   ├── AddVersionForm.tsx     # Form: name + path inputs
│   │   │   ├── SettingsPanel.tsx      # Env var name setting
│   │   │   └── SetupGuide.tsx         # Oracle link + instructions
│   │   ├── hooks/
│   │   │   └── useVersions.ts         # API interaction hook
│   │   └── types/
│   │       └── index.ts
│   ├── vite.config.ts
│   ├── tsconfig.json
│   └── package.json
```

### Backend Requirements

- **Runtime:** Node.js 18+
- **Framework:** Express 4.x with TypeScript (strict mode)
- **Port:** Read from `process.env.PORT`, fallback to `3001`
- **CORS:** Allow `localhost` origins during development
- **Error handling:** All route handlers must return structured JSON error responses `{ error: string, details?: string }`
- **Config file location:** Use `os.homedir()` + `.java-context-switcher/java-versions.md` as the default path

### Config File Format (`java-versions.md`)

```markdown
# Java Context Switcher Config

## Settings
- envVarName: JAVA_HOME

## Versions
- id: uuid-1
  name: Java 21 LTS
  path: C:\Program Files\Java\jdk-21
- id: uuid-2
  name: Java 17
  path: C:\Program Files\Java\jdk-17
```

- Each version entry has: `id` (UUID v4), `name` (string), `path` (string)
- Settings section holds `envVarName` (defaults to `JAVA_HOME`)
- Parser must handle missing sections gracefully (empty arrays / defaults)
- Writer must preserve human-readable formatting

### Batch Script Execution (`batchScript.ts`)

- Generate a temporary `.bat` file using `os.tmpdir()`
- Script content: `@echo off\nsetx [ENV_VAR_NAME] "[JDK_PATH]"`
- Execute via `child_process.exec` with a 10-second timeout
- Delete the temp file after execution completes (success or failure)
- Return `{ success: boolean, message: string }` to the caller
- **Security:** Validate that the path does not contain shell injection characters before writing the script. Reject paths containing `&`, `|`, `;`, `>`, `<`, `` ` ``, `$`, `(`, `)` or newlines.

### Path Validation

- Before saving a new version entry, verify the directory exists using `fs.existsSync(path)`
- Return a 400 error if the directory does not exist
- Reject paths exceeding 260 characters (Windows MAX_PATH)

### Frontend Requirements

- **Build tool:** Vite 5.x
- **Framework:** React 18 with TypeScript (strict mode)
- **Styling:** Bootstrap 5 via CDN or npm + React-Bootstrap components
- **State management:** React `useState` / `useEffect` + custom hooks; no external state library
- **API calls:** Native `fetch` with async/await; base URL from `import.meta.env.VITE_API_BASE_URL` (defaults to `http://localhost:3001`)
- **Active version indicator:** Store active version id in `localStorage` keyed to `activeJavaVersionId`; apply `active` Bootstrap class to the matching button
- **Toast notifications:** Bootstrap `Toast` component shown on switch success/failure

### UI/UX Specifications

- **Version buttons:** `btn btn-outline-primary` (inactive), `btn btn-primary` (active); display the version name; include a small delete icon (×) on hover
- **Add Version form:** Two text inputs (`Version Name`, `JDK Path`), a `Browse` note (manual path entry only — no OS file picker in this phase), a `Save` button, and inline validation error messages
- **Settings panel:** A collapsible section at the bottom of the page with a labeled text input for the env var name and a `Save` button
- **Setup guide:** A collapsible `accordion` section with the Oracle download link (opens in new tab) and numbered setup steps
- **Layout:** Single-page layout; Bootstrap container; responsive grid for version buttons (2–3 columns on desktop, 1 on mobile)

### Environment Variable Switching UX Note

- After a successful switch, display a warning toast: "Open a new terminal window for the change to take effect."
- The `setx` command only affects new processes; existing terminals are not updated.

## External Dependencies

- **uuid** (`npm install uuid @types/uuid`) — Generate UUID v4 for version entry IDs
  - **Justification:** Stable, widely used, zero-config UUID generation; prevents ID collisions in the config file.
