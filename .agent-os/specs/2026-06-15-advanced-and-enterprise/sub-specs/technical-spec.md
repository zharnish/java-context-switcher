# Technical Specification

This is the technical specification for the spec detailed in .agent-os/specs/2026-06-15-advanced-and-enterprise/spec.md

---

## Technical Requirements

### Config File Schema Extension

Extend `java-versions.md` to support the new fields introduced in this phase:

```markdown
# Java Context Switcher Config

## Settings
- envVarName: JAVA_HOME
- configFilePath: C:\Users\dev\.java-context-switcher\java-versions.md
- teamDefaultsSource: https://example.com/team-java-config.md

## Versions
- id: uuid-1
  name: Java 21 LTS
  path: C:\Program Files\Java\jdk-21
  notes: Used for Spring Boot 3 projects
  order: 0
- id: uuid-2
  name: Java 17
  path: C:\Program Files\Java\jdk-17
  notes: Legacy microservices
  order: 1
```

- `envVarName` — unchanged from Phase 1; single variable name, defaults to `JAVA_HOME`
- `notes` — optional, free text, max 500 characters
- `order` — integer, 0-based; determines display order of buttons

### Cross-Platform Script Execution

- In `batchScript.ts`, check `process.platform` at runtime:
  - `'win32'` → generate and execute `.bat` with `setx [envVarName] "[path]"`
  - `'darwin'` or `'linux'` → delegate to `shellScript.ts`
- `shellScript.ts`:
  - Write a `.sh` file to `os.tmpdir()`
  - Script content: `export [envVarName]="[path]"` appended to the user's shell profile via `>> ~/.profile` (or `~/.zshrc` if `$SHELL` contains `zsh`)
  - Execute via `child_process.exec('bash [script]')` with 10-second timeout
  - Return success message instructing user to run `source ~/.profile` or open a new terminal
- **Security:** Apply same injection-character validation to paths on all platforms

### Drag-to-Reorder

- Install `@dnd-kit/core` and `@dnd-kit/sortable` in the frontend
- Wrap `VersionList` in a `DndContext` + `SortableContext`
- Each `VersionButton` becomes a `useSortable` item
- On drag end, call `PUT /api/versions/reorder` with the new ordered array of IDs
- Backend updates `order` field for all affected entries and rewrites config file

### Import/Export Config

**Export:**
- `GET /api/config/export` — streams the raw `java-versions.md` file as a download with `Content-Disposition: attachment; filename="java-versions.md"`

**Import:**
- `POST /api/config/import` — accepts `multipart/form-data` with a `file` field
- Parse the uploaded Markdown using the existing config parser
- Support two modes (query param `?mode=merge|replace`):
  - `merge`: add imported versions not already present by ID; skip duplicates
  - `replace`: overwrite entire versions list (settings preserved)
- Validate file size (max 100KB) and MIME type (`text/plain`, `text/markdown`)

### Team Defaults Layer

- On app startup, if `teamDefaultsSource` is set in settings, fetch the Markdown file from that URL or local path
- Parse it as a read-only config layer using the same parser
- Merge strategy: team default entries appear first in the list; personal entries follow
- Team entries are flagged with `isTeamDefault: true` in the API response
- Frontend renders team default buttons with a `LockClosedIcon` and no delete control
- Team defaults are never written to the local config file

### Audit Log

- Log file location: same directory as `java-versions.md`, named `switch-audit.log`
- Format: one JSON line per event: `{ "timestamp": "ISO8601", "versionName": "...", "path": "...", "envVarName": "...", "success": true, "error": null }`
- Write appended to the log file after every `POST /api/switch`, regardless of success or failure
- Expose `GET /api/audit` endpoint that returns the last 100 entries as a JSON array (parsed from log file)
- Frontend: add an "Audit Log" collapsible accordion section in the UI showing a table of recent switches

### Config File Path Override

- `PUT /api/settings` extended to accept `configFilePath` string
- Validate: must be an absolute path, parent directory must exist, filename must end in `.md`
- When updated, copy the current config file to the new location, then update the in-memory path used by all subsequent reads/writes
- Backend holds current config path in a module-level variable initialized from settings at startup

## External Dependencies

- **@dnd-kit/core + @dnd-kit/sortable** (`npm install @dnd-kit/core @dnd-kit/sortable`) — Accessible drag-and-drop primitives for React
  - **Justification:** Headless, accessible, and framework-agnostic; lighter than `react-beautiful-dnd` (which is unmaintained); works with Bootstrap layout.
- **multer** (`npm install multer @types/multer`) — Multipart form-data parser for file uploads (config import)
  - **Justification:** Standard Express middleware for `multipart/form-data`; handles file size limits and MIME type validation out of the box.
