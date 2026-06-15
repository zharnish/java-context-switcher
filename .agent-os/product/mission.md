# Product Mission

## Pitch

Java Context Switcher is a localhost web application that helps Java developers on Windows instantly switch between installed Java versions by clicking a button, automatically updating their configured environment variable (e.g. `JAVA_HOME`) via a background batch script.

## Users

### Primary Customers

- **Individual Java Developers:** Developers working locally on Windows who manage multiple Java versions for different projects or environments.
- **Dev Teams & Organizations:** Teams that standardize tooling and need members to easily context-switch between JDK versions without manual PATH editing.

### User Personas

**The Multi-Project Developer** (25–45 years old)
- **Role:** Software Engineer / Java Developer
- **Context:** Works on multiple Java projects simultaneously — some require Java 8, others Java 17 or 21. Must frequently switch environments.
- **Pain Points:** Manually editing `JAVA_HOME` in System Properties is tedious and error-prone; forgetting to switch breaks builds silently.
- **Goals:** Switch Java versions in under 5 seconds; always know which version is currently active.

**The DevOps / Build Engineer** (28–50 years old)
- **Role:** Build Engineer / DevOps
- **Context:** Maintains CI/CD pipelines and local build environments across several Java versions and needs quick environment resets.
- **Pain Points:** Inconsistent environments across machines; no simple UI to verify or change the active JDK.
- **Goals:** Standardize version switching; reduce onboarding friction for new team members.

## The Problem

### Manual Environment Variable Switching Is Error-Prone

Developers with multiple JDK versions must manually navigate to System Properties, find `JAVA_HOME`, and update its value. This process is slow and easy to get wrong, causing broken builds and confusing runtime errors.

**Our Solution:** A single-click UI that triggers a batch script to set the correct environment variable instantly.

### No Centralized View of Installed Java Versions

There is no built-in Windows UI to list, label, and switch between installed JDK paths. Developers must remember or look up installation directories.

**Our Solution:** A persistent, user-curated list of named Java version buttons tied to specific install paths, stored in a local config file.

### Onboarding New Developers Takes Too Long

New team members must be instructed on where to download Java, how to install it, and how to configure `JAVA_HOME` — a process with no single authoritative resource.

**Our Solution:** Built-in download link to Oracle's official Java downloads page plus step-by-step setup instructions embedded in the app.

## Differentiators

### Zero Infrastructure Required

Unlike version managers like `jenv` or `SDKMAN`, Java Context Switcher requires no shell plugin, no PATH manipulation scripts, and no external process manager. It runs as a local web app via `npx` and uses a plain config file.

This results in a tool any developer can spin up in seconds without admin rights or global tool installs.

### Visual, Click-Based Interface

Unlike CLI-based Java switchers, Java Context Switcher provides a graphical browser-based interface accessible at `localhost:3001`. Developers can see all their configured versions at a glance and switch with one click.

This results in dramatically lower cognitive load and eliminates the need to remember CLI commands.

### Fully Customizable Environment Variable Target

Unlike tools that hard-code `JAVA_HOME`, Java Context Switcher allows users to configure which environment variable name is used (defaulting to `JAVA_HOME`), supporting non-standard team configurations.

This results in compatibility with any team or project convention without code changes.

## Key Features

### Core Features

- **Environment Variable Configuration:** Users can set and override the target environment variable name (defaults to `JAVA_HOME`) used when switching Java versions.
- **Java Version Buttons:** Each installed Java version appears as a labeled button; clicking it immediately switches the active version by running a batch script.
- **Add Java Version:** A `+ Add Java Version` button lets users manually enter a JDK installation path and assign a display name to create a new version button.
- **Persistent Config Storage:** All version button configurations (name + path) are saved to a local Markdown-formatted text file so they persist across app restarts.
- **Batch Script Execution:** Clicking a version button triggers a Windows batch script that sets the configured environment variable to the selected JDK path at the system level.

### Setup & Guidance Features

- **Oracle Download Link:** The app surfaces a direct link to [Oracle Java Downloads](https://www.oracle.com/java/technologies/downloads/) so users can find and download JDK packages without leaving the workflow.
- **Setup Instructions:** Step-by-step in-app guidance explains how to download a Java package, install it, and locate the installation directory to use as a path.
- **Active Version Indicator:** The currently active Java version button is visually highlighted so users always know which version is in effect.
- **Delete Version Button:** Users can remove a configured version button when it is no longer needed, updating the config file accordingly.

### Configuration Features

- **Configurable Port:** The app defaults to port `3001` but allows users to override this via an environment variable or config option.
- **Config File Transparency:** The plain-text Markdown config file is human-readable and editable outside the app, giving power users direct access to their configuration.
