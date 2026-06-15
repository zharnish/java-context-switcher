# Spec Tasks

## Tasks

- [ ] 1. Extend config file schema and service for new fields
  - [ ] 1.1 Write tests for updated config parser: `notes`, `order`, `configFilePath`, `teamDefaultsSource`
  - [ ] 1.2 Update `configFile.ts` parser to read and write `notes`, `order` per version entry
  - [ ] 1.3 Update `configFile.ts` writer to serialize new fields back to Markdown format
  - [ ] 1.4 Add `configFilePath` and `teamDefaultsSource` to settings read/write
  - [ ] 1.5 Update `JavaVersion` and `AppSettings` TypeScript interfaces in `types/index.ts`
  - [ ] 1.6 Verify all config file tests pass; verify existing Phase 1 route tests still pass

- [ ] 2. Implement cross-platform script execution
  - [ ] 2.1 Write tests for `shellScript.ts` (mocked `exec`) — valid path, injection chars, timeout
  - [ ] 2.2 Create `shellScript.ts` — detect `$SHELL`, write `export [envVarName]="[path]"` to shell profile, execute via `bash`
  - [ ] 2.3 Add platform detection in `batchScript.ts`: delegate to `shellScript.ts` on `darwin`/`linux`
  - [ ] 2.4 Verify all script tests pass

- [ ] 3. Implement audit log
  - [ ] 3.1 Write tests for audit log write (success + failure) and `GET /api/audit` (returns last 100, handles missing file)
  - [ ] 3.2 Create `src/services/auditLog.ts` — `appendEntry(event)` appends JSON line to `switch-audit.log` in config directory
  - [ ] 3.3 Call `auditLog.appendEntry()` in the `POST /api/switch` route handler after script execution (regardless of outcome)
  - [ ] 3.4 Create `GET /api/audit` route — read log file, parse last 100 lines, return as JSON array; return empty array with `warning` if file missing
  - [ ] 3.5 Verify all audit log tests pass

- [ ] 4. Implement config import/export API
  - [ ] 4.1 Write tests for `GET /api/config/export` (file streamed) and `POST /api/config/import` (merge + replace modes, oversized file, bad MIME)
  - [ ] 4.2 Install `multer`; create `GET /api/config/export` route streaming the config file with correct headers
  - [ ] 4.3 Create `POST /api/config/import` route — validate file size (100KB) and MIME type; parse uploaded file; apply merge or replace strategy
  - [ ] 4.4 Implement merge strategy: add imported versions not already present by ID; skip duplicates
  - [ ] 4.5 Implement replace strategy: overwrite versions list; preserve settings section
  - [ ] 4.6 Verify all import/export tests pass

- [ ] 5. Implement reorder and version notes API
  - [ ] 5.1 Write tests for `PUT /api/versions/reorder` (valid reorder, unknown IDs, missing IDs) and `PUT /api/versions/:id` (notes update)
  - [ ] 5.2 Create `PUT /api/versions/reorder` route — validate `orderedIds` array matches current IDs exactly; update `order` field on each entry; write config
  - [ ] 5.3 Create `PUT /api/versions/:id` route — update `notes` field (max 500 chars); write config
  - [ ] 5.4 Verify all route tests pass

- [ ] 6. Implement team defaults layer
  - [ ] 6.1 Write tests for team defaults fetch (URL, local path, unreachable source, invalid Markdown)
  - [ ] 6.2 Create `src/services/teamDefaults.ts` — `loadTeamDefaults(source)` fetches and parses Markdown from URL (via `fetch`) or local file path
  - [ ] 6.3 On server start, if `teamDefaultsSource` is set, load team defaults into memory; re-fetch on settings update
  - [ ] 6.4 Update `GET /api/versions` to merge team default entries (flagged `isTeamDefault: true`) before personal entries
  - [ ] 6.5 Ensure `DELETE /api/versions/:id` rejects deletion of team default entries with a 403 response
  - [ ] 6.6 Verify all team defaults tests pass

- [ ] 7. Implement config file path override
  - [ ] 7.1 Write tests for config path change: valid new path, non-existent parent directory, non-.md extension
  - [ ] 7.2 Update `PUT /api/settings` to handle `configFilePath`: validate absolute path, parent exists, `.md` extension
  - [ ] 7.3 Copy current config file to new path on change; update in-memory config path variable
  - [ ] 7.4 Verify config is read from new location on subsequent requests

- [ ] 8. Implement drag-to-reorder frontend UI
  - [ ] 8.1 Install `@dnd-kit/core` and `@dnd-kit/sortable` in frontend
  - [ ] 8.2 Wrap `VersionList` in `DndContext` + `SortableContext`; make each `VersionButton` a `useSortable` item
  - [ ] 8.3 On drag end, call `PUT /api/versions/reorder` with new ID order; optimistically update local state
  - [ ] 8.4 Verify reorder persists after page refresh

- [ ] 9. Implement version notes, import/export, audit log, and settings UI
  - [ ] 9.1 Add notes tooltip to `VersionButton`: show `notes` in a Bootstrap `Tooltip` on hover if present
  - [ ] 9.2 Add inline notes edit: clicking an edit icon on `VersionButton` opens a small text input; saving calls `PUT /api/versions/:id`
  - [ ] 9.3 Add Export button in Settings panel: triggers `GET /api/config/export` download
  - [ ] 9.4 Add Import section in Settings panel: file input + merge/replace radio; submits to `POST /api/config/import`; shows result toast
  - [ ] 9.5 Add `teamDefaultsSource` text input in Settings panel
  - [ ] 9.6 Add Audit Log accordion section: table of last 100 entries from `GET /api/audit` (timestamp, version, success)
  - [ ] 9.7 Verify all features end-to-end in the browser; verify macOS/Linux switch generates shell script (smoke test)
