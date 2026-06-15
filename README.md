# java-context-switcher
A UI that allows the user to quickly switch between Java Versions they need to build their apps.

<!-- Modified by AI on 06/15/2026. Edit #2. -->

## Prerequisites

- [Node.js](https://nodejs.org/) v18 or higher (includes npm)
- [Git](https://git-scm.com/download/win)
- Multiple Java JDK installations on your machine

---

## Getting the Installer (First-Time Setup)

### Step 1 — Clone the repository
```bash
git clone https://github.com/zharnish/java-context-switcher.git
cd java-context-switcher
```

### Step 2 — Install all dependencies
```bash
npm run install:all
```

### Step 3 — Build the Windows installer
```bash
npm run dist
```

This compiles the backend and frontend, then packages everything into a Windows installer.

### Step 4 — Run the installer
Open the `dist-electron` folder and run:
```
Java Context Switcher Setup 1.0.0.exe
```

Follow the on-screen prompts to install.

---

## Using the App

Once installed, the app runs in the **system tray** (bottom-right of your taskbar).

| Action | Result |
|---|---|
| **Left-click** the tray icon | Opens the full UI window |
| **Right-click** the tray icon | Shows quick menu: open, switch Java version, quit |
| **Click a version** in the right-click menu | Switches your `JAVA_HOME` immediately |

The app registers itself to **start automatically with Windows** — no manual launch needed after reboot.

---

## Development Mode

To run the app without building an installer (live reload):

```bash
npm run dev
```

- **Frontend** → [http://localhost:5173](http://localhost:5173)
- **Backend API** → [http://localhost:3001](http://localhost:3001)

To run as an Electron desktop app in dev mode:
```bash
npm run electron:dev
```

---

## Running Tests

```bash
npm test
```

