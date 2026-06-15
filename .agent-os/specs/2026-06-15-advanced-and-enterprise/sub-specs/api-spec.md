# API Specification

This is the API specification for the spec detailed in .agent-os/specs/2026-06-15-advanced-and-enterprise/spec.md

---

## Base URL

`http://localhost:3001/api`

---

## New / Modified Endpoints

### PUT /api/settings (modified)

**Purpose:** Update app settings — now also supports `configFilePath` and `teamDefaultsSource` in addition to the existing `envVarName`.
**Parameters (JSON body):**
- `envVarName` (string, optional) — The environment variable name to set on switch (e.g. `JAVA_HOME`). Already supported in Phase 1; included here for completeness.
- `configFilePath` (string, optional) — Absolute path to a new config file location. Parent directory must exist; filename must end in `.md`.
- `teamDefaultsSource` (string, optional) — URL or absolute path to a read-only team defaults Markdown config file.

**Response:**
```json
{
  "settings": {
    "envVarName": "JAVA_HOME",
    "configFilePath": "C:\\Users\\dev\\.java-context-switcher\\java-versions.md",
    "teamDefaultsSource": null
  }
}
```
**Errors:**
- `400` — Invalid `configFilePath` (not absolute, parent missing, wrong extension)
- `500` — Config file copy or write failed

---

### PUT /api/versions/reorder

**Purpose:** Update the display order of all version buttons.
**Parameters (JSON body):**
- `orderedIds` (string[], required) — Full array of version IDs in the desired display order.

**Validation:**
- Array must contain exactly the same IDs as currently stored (no additions or deletions)

**Response:**
```json
{ "success": true }
```
**Errors:**
- `400` — `orderedIds` is missing, not an array, or contains unknown/missing IDs
- `500` — Config file could not be written

---

### PUT /api/versions/:id (new)

**Purpose:** Update a version entry's `notes` field (and in future any other mutable field).
**Parameters (JSON body):**
- `notes` (string, optional) — Free text notes for the version entry, max 500 characters.

**Response:**
```json
{
  "version": { "id": "uuid-1", "name": "Java 21 LTS", "path": "C:\\...", "notes": "Spring Boot 3 projects", "order": 0 }
}
```
**Errors:**
- `404` — Version entry not found
- `400` — `notes` exceeds 500 characters
- `500` — Config file write failed

---

### GET /api/config/export

**Purpose:** Stream the current `java-versions.md` config file as a downloadable file.
**Parameters:** None
**Response:** Raw file content with headers:
- `Content-Type: text/markdown`
- `Content-Disposition: attachment; filename="java-versions.md"`

**Errors:**
- `500` — Config file could not be read

---

### POST /api/config/import

**Purpose:** Import a `java-versions.md` file to merge or replace the current version list.
**Parameters:**
- `file` (multipart/form-data, required) — The Markdown config file to import. Max size: 100KB. Accepted types: `text/plain`, `text/markdown`.
- `mode` (query param) — `merge` (default) or `replace`

**Response:**
```json
{
  "success": true,
  "imported": 3,
  "skipped": 1,
  "message": "Imported 3 versions. 1 duplicate skipped."
}
```
**Errors:**
- `400` — No file uploaded
- `400` — File exceeds 100KB
- `400` — Invalid MIME type
- `400` — File could not be parsed as a valid config
- `500` — Config file write failed

---

### GET /api/versions (modified)

**Purpose:** Returns all saved Java version entries — now includes `notes`, `order`, and `isTeamDefault` fields.
**Response:**
```json
{
  "versions": [
    { "id": "uuid-1", "name": "Java 21 LTS", "path": "C:\\...", "notes": "Spring Boot 3", "order": 0, "isTeamDefault": false },
    { "id": "team-uuid-1", "name": "Java 8 (Team)", "path": "C:\\...", "notes": null, "order": -1, "isTeamDefault": true }
  ]
}
```

---

### GET /api/audit

**Purpose:** Return the last 100 version switch events from the audit log.
**Parameters:** None
**Response:**
```json
{
  "entries": [
    {
      "timestamp": "2026-06-15T14:30:00.000Z",
      "versionName": "Java 21 LTS",
      "path": "C:\\Program Files\\Java\\jdk-21",
      "envVarName": "JAVA_HOME",
      "success": true,
      "error": null
    }
  ]
}
```
**Errors:**
- `500` — Audit log file could not be read (returns empty array with `warning` field instead of 500)
