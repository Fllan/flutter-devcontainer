# flutter-devcontainer

A zero-config [Dev Container](https://containers.dev/) for Flutter & Dart development. Open any project with this `.devcontainer/` and get a fully working environment — `flutter doctor -v` passes with **zero errors, zero warnings**.

Built for the AI-assisted development era: works out of the box with [GitHub Copilot](https://github.com/features/copilot), [Kiro CLI](https://kiro.dev), [Gemini in Android Studio](https://developer.android.com/studio/preview/gemini), or any AI coding agent that supports devcontainers.

## Why use a Dev Container?

If you're new to coding — or you're a vibecoder building apps with AI — this section is for you.

**The problem:** Setting up Flutter on your machine means installing the SDK, Android SDK, Java, Chrome, Linux libraries, accepting licenses, configuring PATH variables... It takes hours, breaks in weird ways, and differs on every machine. When you ask an AI agent to help, it can't fix your local setup — it doesn't have access to your system.

**The solution:** A Dev Container packages your entire development environment into a container. Think of it as a pre-built computer inside your computer, with everything already installed and configured.

### Why this matters when working with AI

| Without a devcontainer | With a devcontainer |
|---|---|
| "It works on my machine" | It works on **every** machine |
| AI suggests a fix but your setup is different | AI and you share the **exact same environment** |
| Hours spent debugging install issues | Zero setup — open and code |
| Your AI agent can't install tools for you | The container **already has everything** |
| Broke your system installing dependencies | Your system stays **untouched** |

When you give an AI agent a devcontainer, you're giving it a **predictable, reproducible workspace**. The agent knows exactly what tools are available, what versions are installed, and what commands will work. No guessing, no "can you run `flutter doctor` and paste the output?".

### How it works (the simple version)

1. You have a project folder with a `.devcontainer/` directory inside it
2. You open it in VS Code → it asks "Reopen in Container?" → you click yes
3. VS Code builds a container with Flutter, Dart, Android SDK, Chrome — everything
4. You (and your AI agent) code inside this container
5. Your files are still on your machine — the container just provides the tools

**You don't need to understand Docker.** You don't need to install Flutter. You just need VS Code and the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers).

## What's inside

| Component | Version | Purpose |
|---|---|---|
| Flutter | 3.41.7 | SDK (stable channel) |
| Dart | 3.11.5 | Bundled with Flutter |
| Android SDK | 36 | Platform, build-tools, cmdline-tools |
| OpenJDK | 17 | Android toolchain |
| Google Chrome | latest | Web development & testing |
| Linux toolchain | clang 18, cmake, ninja, pkg-config, GTK3 | Desktop development |

## Quick start

```bash
# 1. Copy .devcontainer/ into your project
cp -r .devcontainer/ /path/to/your/flutter/project/

# 2. Open in VS Code
cd /path/to/your/flutter/project
code .

# 3. VS Code will prompt: "Reopen in Container" → click it
#    Or: Ctrl+Shift+P → "Dev Containers: Reopen in Container"
```

That's it. `flutter doctor -v` runs automatically on first start.

## Works with

- **VS Code** + Dev Containers extension
- **GitHub Codespaces** — push this repo, open in Codespaces, start coding
- **Any devcontainer-compatible tool** — DevPod, JetBrains, CLI (`devcontainer up`)
- **AI agents** — Copilot Workspace, Kiro, Cursor, Windsurf, or any agent that can spin up a devcontainer

## Targets

| Target | Status | Notes |
|---|---|---|
| Android | ✅ | SDK + licenses accepted |
| Web | ✅ | Chrome installed |
| Linux desktop | ✅ | Full GTK3 toolchain |
| iOS / macOS | ❌ | Requires macOS host |
| Windows | ❌ | Requires Windows host |

> **Need iOS, macOS, or Windows builds?** Flutter compiles native code for these platforms, which requires their native toolchains — Xcode on macOS, Visual Studio on Windows. These can't run inside a Linux container. You can still **write and test all your Dart/Flutter code** in this devcontainer (logic, UI, state management, API calls), then build the platform-specific binaries on the target OS. In practice: use this devcontainer for development, then use [Codemagic](https://codemagic.io), [GitHub Actions](https://github.com/subosito/flutter-action), or a macOS/Windows machine for the final build step only.

## Updating versions

Edit the `ARG` values at the top of `.devcontainer/Dockerfile`:

```dockerfile
ARG FLUTTER_VERSION=3.41.7
ARG ANDROID_SDK_CMDLINE_TOOLS_VERSION=14742923
ARG ANDROID_BUILD_TOOLS_VERSION=36.0.0
ARG ANDROID_PLATFORM_VERSION=36
```

Check for updates at (official sources only):
- **Flutter** — https://docs.flutter.dev/install/archive
- **Android cmdline-tools** — https://developer.android.com/studio#command-line-tools-only
- **Build-tools** — https://developer.android.com/tools/releases/build-tools
- **Platforms** — https://developer.android.com/tools/releases/platforms

Then rebuild: `Dev Containers: Rebuild Container` in VS Code.

## License

MIT
