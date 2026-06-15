# API Specification

This is the API specification for the spec detailed in .agent-os/specs/2026-06-15-core-mvp-and-guidance/spec.md

---

## Base URL

`http://localhost:3001/api`

---

## Endpoints

### GET /api/versions

**Purpose:** Return all saved Java version entries from the config file.
**Parameters:** None
**Response:**
```json
{
  "versions": [
    { "id": "uuid-1", "name": "Java 21 LTS", "path": "C:\\Program Files\\Java\\jdk-21" },
    { "id": "uuid-2", "name": "Java 17", "path": "C:\\Program Files\\Java\\jdk-17" }
  ]
}
```
**Errors:**
- `500` — Config file could not be read

---

### POST /api/versions

**Purpose:** Add a new Java version entry to the config file.
**Parameters (JSON body):**
- `name` (string, required) — Display name for the version button
- `path` (string, required) — Absolute path to the JDK installation directory

**Validation:**
- `name` must be non-empty, max 100 characters
- `path` must be non-empty, max 260 characters, directory must exist on disk, must not contain shell injection characters

**Response:**
```json
{
  "version": { "id": "uuid-new", "name": "Java 21 LTS", "path": "C:\\Program Files\\Java\\jdk-21" }
}
```
**Errors:**
- `400` — Missing or invalid `name` or `path`
- `400` — Directory does not exist
- `400` — Path contains disallowed characters
- `500` — Config file could not be written

---

### DELETE /api/versions/:id

**Purpose:** Remove a Java version entry from the config file by ID.
**Parameters:**
- `:id` (URL param) — UUID of the version entry to delete

**Response:**
```json
{ "success": true }
```
**Errors:**
- `404` — Version entry with given ID not found
- `500` — Config file could not be written

---

### POST /api/switch

**Purpose:** Execute a Windows batch script to set the configured environment variable to the selected JDK path.
**Parameters (JSON body):**
- `id` (string, required) — UUID of the version entry to switch to

**Response:**
```json
{
  "success": true,
  "message": "JAVA_HOME set to C:\\Program Files\\Java\\jdk-21. Open a new terminal for the change to take effect."
}
```
**Errors:**
- `400` — Missing or invalid `id`
- `404` — Version entry with given ID not found
- `500` — Batch script execution failed (includes `details` field with stderr output)

---

### GET /api/settings

**Purpose:** Return the current app settings (env var name, etc.).
**Parameters:** None
**Response:**
```json
{
  "settings": {
    "envVarName": "JAVA_HOME"
  }
}
```
**Errors:**
- `500` — Config file could not be read

---

### PUT /api/settings

**Purpose:** Update app settings and persist them to the config file.
**Parameters (JSON body):**
- `envVarName` (string, required) — The environment variable name to use for switching (e.g. `JAVA_HOME`, `JDK_HOME`)

**Validation:**
- `envVarName` must match pattern `^[A-Z_][A-Z0-9_]*$` (Windows env var naming rules), max 64 characters

**Response:**
```json
{
  "settings": {
    "envVarName": "JDK_HOME"
  }
}
```
**Errors:**
- `400` — Missing or invalid `envVarName`
- `500` — Config file could not be written
