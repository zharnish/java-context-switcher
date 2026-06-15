# Technical Stack

## Application Framework
Node.js + Express (TypeScript) — backend API server

## Frontend Framework
React 18 (TypeScript)

## JavaScript / TypeScript Strategy
TypeScript throughout — strict mode enabled for both frontend and backend

## CSS Framework
Bootstrap 5

## UI Component Library
React-Bootstrap

## Icons
Heroicons (via @heroicons/react)

## Fonts Provider
System fonts / Bootstrap defaults (no external font CDN)

## Storage / Persistence
Local Markdown-formatted text file (`java-versions.md`) stored in the user's app config directory — no database

## Port
3001 (default, overridable via `PORT` environment variable)

## Application Hosting
Localhost only — runs on the developer's machine via `npx java-context-switcher`

## Database Hosting
n/a — file-based storage only

## Asset Hosting
Bundled with the app — no external asset hosting

## Deployment / Distribution
Published to npm; run via `npx java-context-switcher` — no install required

## Build Tool
Vite (frontend) + tsc (backend)

## Platform Target
Windows only (batch script execution for environment variable switching)

## Code Repository URL
https://github.com/zharnish/java-context-switcher
