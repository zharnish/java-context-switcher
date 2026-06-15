# Spec Tasks

## Tasks

- [ ] 1. Configure unified project build and npm package structure
  - [ ] 1.1 Create root `package.json` with `bin`, `files`, `main`, `engines`, `build`, and `prepare` scripts
  - [ ] 1.2 Configure `frontend/vite.config.ts` to output to `../dist/public` and set `base: '/'`
  - [ ] 1.3 Configure `backend/tsconfig.json` to output to `../dist/`
  - [ ] 1.4 Add Vite dev proxy: forward `/api/*` to `http://localhost:3001` for local development
  - [ ] 1.5 Run `npm run build` end-to-end and verify `dist/` contains both server files and `public/` frontend assets
  - [ ] 1.6 Verify all existing backend tests still pass after tsconfig output change

- [ ] 2. Wire Express to serve frontend static files
  - [ ] 2.1 Add `express.static(path.join(__dirname, 'public'))` middleware in `server.ts` before API routes
  - [ ] 2.2 Add SPA catch-all route after all API routes: `GET *` → serve `public/index.html`
  - [ ] 2.3 Verify that opening `http://localhost:3001` after a production build loads the React app
  - [ ] 2.4 Verify API routes at `/api/*` still respond correctly (not caught by static/catch-all)

- [ ] 3. Implement npx entry point and auto-open browser
  - [ ] 3.1 Install `open` package in backend; add to dependencies
  - [ ] 3.2 In `server.ts` `listen` callback, call `open()` guarded by `NO_OPEN` env var and `require.main === module` check
  - [ ] 3.3 Test `npm link` locally; run `java-context-switcher` in a new terminal and verify browser opens to `localhost:3001`
  - [ ] 3.4 Verify `NO_OPEN=true java-context-switcher` suppresses browser open (for CI)

- [ ] 4. Integrate Heroicons into the frontend UI
  - [ ] 4.1 Verify `@heroicons/react` is installed (added in Phase 1); if not, install it
  - [ ] 4.2 Replace `+` Add Version text button with `PlusCircleIcon` (24px) + "Add Java Version" label
  - [ ] 4.3 Replace delete text controls on `VersionButton` with `TrashIcon` (16px); show on hover via CSS class toggle
  - [ ] 4.4 Add `CheckCircleIcon` (16px) inside active `VersionButton` alongside version name
  - [ ] 4.5 Add `ArrowPathIcon` with CSS `animate-spin` class during in-progress switch operations
  - [ ] 4.6 Ensure all icons have `aria-hidden="true"` and adjacent accessible text or `title`

- [ ] 5. Harden error handling — backend
  - [ ] 5.1 Write tests for global error handler and auto-create-config-on-missing behavior
  - [ ] 5.2 Wrap all route handler bodies in try/catch returning `{ error, details }` JSON on failure
  - [ ] 5.3 Add global Express error middleware (`app.use((err, req, res, next) => ...)`) as last middleware
  - [ ] 5.4 Update `configFile.ts` `readConfig()` to auto-create file with defaults if not found
  - [ ] 5.5 Verify all error handling tests pass

- [ ] 6. Harden error handling — frontend
  - [ ] 6.1 Update `useVersions.ts` hook: check `response.ok` on all fetch calls; extract `error` field from JSON body on failure
  - [ ] 6.2 Add network error catch block: show "Could not connect to the server. Is it still running?" toast
  - [ ] 6.3 Update `AddVersionForm.tsx` to display API-returned `error` messages inline below the relevant input
  - [ ] 6.4 Add client-side path validation in `AddVersionForm.tsx` (empty, length, injection chars) before API call
  - [ ] 6.5 Verify error scenarios in the browser: invalid path, server stopped, duplicate name

- [ ] 7. Write README and prepare for npm publish
  - [ ] 7.1 Write `README.md` with all required sections: description, prerequisites, quick start, config, how-to, screenshots placeholder, contributing, license
  - [ ] 7.2 Add `LICENSE` file (MIT)
  - [ ] 7.3 Set `"version": "1.0.0"` in root `package.json`; set `"name": "java-context-switcher"`
  - [ ] 7.4 Run `npm pack` and inspect the tarball to verify only `dist/` and `README.md` are included
  - [ ] 7.5 Verify `npx .` (from repo root after pack) launches the app correctly end-to-end
