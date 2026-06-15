# Technical Specification

This is the technical specification for the spec detailed in .agent-os/specs/2026-06-15-polish-and-distribution/spec.md

---

## Technical Requirements

### npm Package Configuration

- Add `"bin": { "java-context-switcher": "./dist/server.js" }` to root `package.json`
- Add `"files": ["dist/"]` to root `package.json` to control published artifacts
- Set `"main": "./dist/server.js"` in root `package.json`
- Add `"engines": { "node": ">=18.0.0" }` constraint
- Add a build script: `"build": "cd frontend && vite build && cd ../backend && tsc"`
- Add a `"prepare"` script that runs the build for `npm publish`
- Compiled output structure:
  ```
  dist/
  ├── server.js          # Compiled backend entry
  ├── routes/
  ├── services/
  ├── types/
  └── public/            # Vite-built frontend static files
  ```

### Frontend Build into Backend

- Configure `vite.config.ts`: set `build.outDir` to `../dist/public` (relative to `frontend/`)
- Update Express `server.ts`: add `express.static(path.join(__dirname, 'public'))` middleware
- Add a catch-all route after API routes: serve `public/index.html` for any non-API GET request (SPA fallback)
- In `frontend/vite.config.ts`, set `base: '/'`
- In development, the Vite proxy (`vite.config.ts` server.proxy) forwards `/api/*` to `localhost:3001`; in production the same Express process handles both

### Auto-Open Browser

- Install `open` npm package (`npm install open`)
- In `server.ts`, after `app.listen` callback fires, call `open(\`http://localhost:${port}\`)`
- Guard with `process.env.NO_OPEN !== 'true'` so CI/testing environments can suppress it
- Only open if running as the `bin` entry (detect via `require.main === module`)

### Heroicons Integration

- Package `@heroicons/react` is already listed in Phase 1 frontend dependencies
- Replace text controls:
  - `+` Add button → `PlusCircleIcon` (24px)
  - Delete button → `TrashIcon` (16px, shown on hover)
  - Active version indicator → `CheckCircleIcon` (16px, shown inside active button)
  - Switch in-progress spinner → `ArrowPathIcon` (16px, animated spin via CSS)
- All icons must have `aria-hidden="true"` and adjacent visible text or a `title` attribute for accessibility

### Error Handling Requirements

**Backend:**
- Wrap all route handlers in try/catch; return structured `{ error: string, details?: string }` JSON
- Add a global Express error handler middleware as the last `app.use` call
- Log errors to `console.error` with timestamp; do not expose stack traces in production responses
- If the config file is missing on a read, auto-create it with defaults rather than returning an error

**Frontend:**
- All `fetch` calls must handle non-2xx responses: extract `error` field from response JSON and display it
- Network failures (fetch throws) must show a toast: "Could not connect to the server. Is it still running?"
- Form validation errors from the API must display inline, not as a generic alert

### Path Validation (Frontend)

- Before submitting the Add Version form, check:
  - Path is not empty
  - Path length does not exceed 260 characters
  - Path does not contain obvious injection characters (`&`, `|`, `;`, `>`, `<`)
- Display inline error without making an API call if client-side check fails
- Note: existence check is server-side only (no OS access from browser)

### README Requirements

Contents of `README.md`:
1. Project name + one-liner description
2. Prerequisites (Node.js 18+, Windows)
3. Quick start: `npx java-context-switcher`
4. Configuration: PORT env var, config file location
5. How to add a Java version (with Oracle download link)
6. How to change the environment variable name
7. Screenshots section (placeholder images until screenshots are taken)
8. Contributing guide (fork, install, `npm run dev`, PR)
9. License

## External Dependencies

- **open** (`npm install open`) — Cross-platform utility to open URLs in the default browser on server start
  - **Justification:** Handles OS-specific `start`, `xdg-open`, `open` commands; avoids reimplementing platform detection.
