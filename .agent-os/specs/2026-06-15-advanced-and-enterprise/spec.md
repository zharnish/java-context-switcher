# Spec Requirements Document

> Spec: Advanced Configuration and Enterprise Features
> Created: 2026-06-15
> Status: Planning

## Overview

Extend Java Context Switcher with power-user configuration capabilities (config portability, version notes, reordering) and team-oriented features (shareable configs, team defaults layer, audit log, cross-platform support) that elevate the tool from a personal utility to a team-ready development workflow aid.

## User Stories

### Power User Configuration

As a power user, I want to override the config file location, attach notes to each version, reorder my version buttons, and import/export my configuration — so that the tool fits my custom workflow without any code changes.

The user opens Settings, optionally changes the config file path, drags buttons into a preferred order, adds notes like "Used for legacy Spring project", and downloads their full config as a Markdown file to share with a colleague.

### Team Configuration Sharing

As a team lead, I want to publish a shared base configuration that all team members can import, so that new developers have a pre-populated set of standard Java version buttons without manual setup.

The team lead exports their config, shares the file or URL, and new developers import it on first launch. Personal additions are layered on top of the team defaults, and the team defaults remain read-only in the UI.

### Audit and Cross-Platform Support

As a developer using macOS or Linux, I want Java Context Switcher to work on my platform using a shell script instead of a batch script, so that I can use the same tool as my Windows colleagues.

On macOS/Linux, clicking a version button runs a shell script that sets the env variable in the user's shell profile (`.bashrc`, `.zshrc`), with a clear instruction to `source` the file or open a new terminal.

## Spec Scope

1. **Config File Path Override** - UI control to change where `java-versions.md` is stored; supports version-controlling the config in a project repo.
2. **Import/Export Config** - Download the current `java-versions.md` as a file; upload a Markdown file to merge or replace the current config.
3. **Version Notes** - Optional freetext notes field per version entry, displayed as a tooltip on the version button.
4. **Drag-to-Reorder** - Version buttons can be reordered by drag-and-drop; order is persisted to config file.
5. **Shareable Config Export** - Generate a shareable config file or URL fragment for team distribution.
6. **Team Defaults Layer** - Read-only config layer loaded from a URL or file path; merged with personal config in the UI.
7. **Audit Log** - Record each version switch event (timestamp, version name, env var, success/failure) in a local log file.
8. **macOS/Linux Support** - Detect OS at runtime; use shell script (`export VAR=path >> ~/.bashrc`) instead of batch script on non-Windows platforms.

## Out of Scope

- GUI installer or system tray integration (deferred indefinitely — out of roadmap for now)
- Cloud sync of config data
- Authentication or multi-user support
- Automated detection of installed JDKs from the filesystem

## Expected Deliverable

1. User can drag a version button to a new position; the order is preserved after restarting the app.
2. User can export their config, delete all entries, re-import the file, and see all entries restored.
3. Team defaults entries appear in the UI with a distinct visual treatment (e.g. lock icon) and cannot be deleted.
4. Each version switch is recorded in the audit log file with timestamp and outcome.
5. Running the app on macOS generates a shell script instead of a `.bat` file; the success toast instructs the user to source their shell profile.
