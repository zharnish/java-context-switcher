# Spec Tasks

## Tasks

- [ ] 1. Initialize and scaffold backend project
  - [ ] 1.1 Create `backend/` directory with `package.json`, `tsconfig.json` (strict mode), and `nodemon` dev setup
  - [ ] 1.2 Install dependencies: `express`, `cors`, `uuid`, `@types/express`, `@types/cors`, `@types/uuid`, `@types/node`, `typescript`, `ts-node`, `nodemon`
  - [ ] 1.3 Create `src/server.ts` — Express app wiring, port from `process.env.PORT` defaulting to `3001`, CORS middleware, JSON body parser, route mounts
  - [ ] 1.4 Create `src/types/index.ts` — `JavaVersion`, `AppSettings`, `ConfigData` TypeScript interfaces
  - [ ] 1.5 Verify server starts on port 3001 with `ts-node src/server.ts` and responds to `GET /health`

- [ ] 2. Implement config file service
  - [ ] 2.1 Write tests for `configFile.ts` — parse, read, write, empty file, missing file, malformed file scenarios
  - [ ] 2.2 Create `src/services/configFile.ts` — `readConfig()`, `writeConfig()`, `getConfigPath()` (uses `os.homedir()`)
  - [ ] 2.3 Implement Markdown parser: extract `Settings` section (envVarName) and `Versions` section (array of id/name/path)
  - [ ] 2.4 Implement Markdown writer: serialize config back to human-readable Markdown format
  - [ ] 2.5 Add file creation on first run (create directory + file if not exists)
  - [ ] 2.6 Verify all config file tests pass

- [ ] 3. Implement batch script execution service
  - [ ] 3.1 Write tests for `batchScript.ts` — valid path, path with injection chars, timeout scenario
  - [ ] 3.2 Create `src/services/batchScript.ts` — `switchVersion(envVarName, jdkPath)` function
  - [ ] 3.3 Implement path sanitization: reject paths with `& | ; > < \` $ ( )` or newlines
  - [ ] 3.4 Generate temp `.bat` file in `os.tmpdir()` with `setx [ENV_VAR] "[PATH]"` content
  - [ ] 3.5 Execute script via `child_process.exec` with 10-second timeout; delete temp file after execution
  - [ ] 3.6 Verify all batch script tests pass

- [ ] 4. Implement REST API routes
  - [ ] 4.1 Write tests for all 6 endpoints (success + error cases) using supertest
  - [ ] 4.2 Create `src/routes/versions.ts` — `GET /api/versions`, `POST /api/versions` (with path validation + UUID gen), `DELETE /api/versions/:id`
  - [ ] 4.3 Create `src/routes/switch.ts` — `POST /api/switch` (look up version, call batchScript service)
  - [ ] 4.4 Create `src/routes/settings.ts` — `GET /api/settings`, `PUT /api/settings` (with envVarName pattern validation)
  - [ ] 4.5 Mount all routes in `server.ts` under `/api`
  - [ ] 4.6 Verify all route tests pass

- [ ] 5. Initialize and scaffold frontend project
  - [ ] 5.1 Create `frontend/` with Vite + React + TypeScript template; configure `VITE_API_BASE_URL` env var
  - [ ] 5.2 Install dependencies: `bootstrap`, `react-bootstrap`, `@heroicons/react`
  - [ ] 5.3 Import Bootstrap CSS in `main.tsx`; set up base `App.tsx` layout with Bootstrap container
  - [ ] 5.4 Create `src/types/index.ts` mirroring backend types
  - [ ] 5.5 Create `src/hooks/useVersions.ts` — `fetchVersions`, `addVersion`, `deleteVersion`, `switchVersion`, `fetchSettings`, `saveSettings` using native `fetch`
  - [ ] 5.6 Verify frontend builds and loads at `localhost:5173` (Vite dev server) proxied to backend

- [ ] 6. Build version button list UI
  - [ ] 6.1 Write tests for `VersionButton` and `VersionList` components (render, active state, click handler)
  - [ ] 6.2 Create `VersionButton.tsx` — Bootstrap `btn btn-outline-primary` / `btn btn-primary` (active); delete icon on hover; click calls `onSwitch`
  - [ ] 6.3 Create `VersionList.tsx` — responsive Bootstrap grid rendering a list of `VersionButton` components
  - [ ] 6.4 Wire active version state: persist active ID to `localStorage`, apply active class on match
  - [ ] 6.5 Verify all component tests pass

- [ ] 7. Build Add Version form
  - [ ] 7.1 Write tests for `AddVersionForm` — render, submit with valid data, submit with invalid data, inline error display
  - [ ] 7.2 Create `AddVersionForm.tsx` — name + path text inputs, Save button, client-side required field validation, inline error messages
  - [ ] 7.3 On successful save, append new button to `VersionList` and clear form inputs
  - [ ] 7.4 Display API-returned validation errors (e.g. "Directory does not exist") inline below path input
  - [ ] 7.5 Verify all form tests pass

- [ ] 8. Build settings panel and setup guide
  - [ ] 8.1 Create `SettingsPanel.tsx` — collapsible Bootstrap accordion; `envVarName` text input with pattern hint, Save button; load current value on mount
  - [ ] 8.2 Create `SetupGuide.tsx` — collapsible Bootstrap accordion; Oracle download link (opens in `_blank` with `rel="noopener noreferrer"`); numbered setup steps (download, install, locate path, add to app)
  - [ ] 8.3 Integrate both panels into `App.tsx` below the version list

- [ ] 9. Implement switch feedback and UX polish
  - [ ] 9.1 Add Bootstrap `Toast` component to `App.tsx` for success/failure notifications after switch
  - [ ] 9.2 Include "Open a new terminal for the change to take effect" message in the success toast
  - [ ] 9.3 Add loading state (disabled button + spinner) during switch operation
  - [ ] 9.4 Verify end-to-end: add a version, switch to it, confirm toast appears, verify `JAVA_HOME` updated in new terminal
