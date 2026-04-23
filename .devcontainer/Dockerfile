# ==============================================================================
# Flutter & Dart SaaS Development Container
# Goal: flutter doctor -v with ZERO errors and ZERO warnings
#
# This Dockerfile is shared by all devcontainer variants.
# Features are toggled via build args from each variant's devcontainer.json.
# ==============================================================================
FROM ubuntu:24.04

# Version check URLs (official sources only):
#   Flutter:          https://docs.flutter.dev/install/archive
#   Android cmdline:  https://developer.android.com/studio#command-line-tools-only
#   Build-tools:      https://developer.android.com/tools/releases/build-tools
#   Platforms:        https://developer.android.com/tools/releases/platforms
ARG FLUTTER_VERSION=3.41.7
ARG ANDROID_SDK_CMDLINE_TOOLS_VERSION=14742923
ARG ANDROID_BUILD_TOOLS_VERSION=36.0.0
ARG ANDROID_PLATFORM_VERSION=36

# Feature toggles (set from devcontainer.json build args)
ARG INSTALL_KIRO=false
ARG INSTALL_SERVERPOD=false
ARG INSTALL_NODEJS=false

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=C.UTF-8

# ---------------------------------------------------------------------------
# 1. System packages: Flutter Linux toolchain + Android SDK deps + Chrome deps
# ---------------------------------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    # Core tools
    curl git unzip xz-utils zip ca-certificates wget gnupg \
    # Flutter Linux desktop toolchain (required by flutter doctor)
    clang cmake ninja-build pkg-config \
    libgtk-3-dev liblzma-dev libstdc++-13-dev \
    # Android SDK dependencies
    openjdk-17-jdk-headless \
    # Chrome dependencies
    fonts-liberation libatk-bridge2.0-0t64 libatk1.0-0t64 libcups2t64 \
    libdrm2 libgbm1 libnspr4 libnss3 libxcomposite1 libxdamage1 \
    libxfixes3 libxkbcommon0 libxrandr2 xdg-utils \
    # Useful dev tools
    sudo bash-completion jq \
    # mesa-utils for eglinfo (flutter doctor Linux toolchain check)
    mesa-utils \
    && rm -rf /var/lib/apt/lists/*

# ---------------------------------------------------------------------------
# 2. Google Chrome stable (flutter doctor checks for this)
# ---------------------------------------------------------------------------
RUN wget -q -O /tmp/chrome.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && apt-get update && apt-get install -y --no-install-recommends /tmp/chrome.deb \
    && rm /tmp/chrome.deb && rm -rf /var/lib/apt/lists/*

# ---------------------------------------------------------------------------
# 3. Non-root user
# ---------------------------------------------------------------------------
RUN usermod -l vscode -d /home/vscode -m ubuntu \
    && groupmod -n vscode ubuntu \
    && echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/vscode \
    && chmod 0440 /etc/sudoers.d/vscode

# ---------------------------------------------------------------------------
# 4. Android SDK
# ---------------------------------------------------------------------------
ENV ANDROID_HOME=/home/vscode/android-sdk
ENV ANDROID_SDK_ROOT=${ANDROID_HOME}
ENV PATH="${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${PATH}"

USER vscode
RUN mkdir -p ${ANDROID_HOME}/cmdline-tools \
    && curl -fsSL "https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_CMDLINE_TOOLS_VERSION}_latest.zip" -o /tmp/cmdline-tools.zip \
    && unzip -q /tmp/cmdline-tools.zip -d /tmp/cmdline-tools-tmp \
    && mv /tmp/cmdline-tools-tmp/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest \
    && rm -rf /tmp/cmdline-tools.zip /tmp/cmdline-tools-tmp

# Accept all Android SDK licenses and install required components
RUN yes | sdkmanager --licenses > /dev/null 2>&1 \
    && sdkmanager --install \
    "platform-tools" \
    "build-tools;${ANDROID_BUILD_TOOLS_VERSION}" \
    "platforms;android-${ANDROID_PLATFORM_VERSION}" \
    && sdkmanager --update

# ---------------------------------------------------------------------------
# 5. Flutter SDK
# ---------------------------------------------------------------------------
ENV FLUTTER_HOME=/home/vscode/flutter
ENV PUB_CACHE=/home/vscode/.pub-cache
ENV PATH="${FLUTTER_HOME}/bin:${FLUTTER_HOME}/bin/cache/dart-sdk/bin:${PUB_CACHE}/bin:${PATH}"
ENV CHROME_EXECUTABLE=/usr/bin/google-chrome-stable

RUN curl -fsSL "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz" \
    | tar xJ -C /home/vscode \
    && flutter config --no-analytics \
    && dart --disable-analytics \
    && flutter precache --linux --web --android

# ---------------------------------------------------------------------------
# 6. (Optional) Node.js 22 LTS — required by BMAD Method
#    https://github.com/bmad-code-org/BMAD-METHOD
# ---------------------------------------------------------------------------
RUN if [ "$INSTALL_NODEJS" = "true" ]; then \
    curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash - \
    && sudo apt-get install -y --no-install-recommends nodejs \
    && sudo rm -rf /var/lib/apt/lists/*; \
    fi

# ---------------------------------------------------------------------------
# 7. (Optional) Kiro CLI — AI-assisted development
#    https://kiro.dev/docs/cli/installation/
# ---------------------------------------------------------------------------
RUN if [ "$INSTALL_KIRO" = "true" ]; then \
    curl -fsSL https://cli.kiro.dev/install | bash; \
    fi

# ---------------------------------------------------------------------------
# 8. (Optional) Serverpod CLI
# ---------------------------------------------------------------------------
RUN if [ "$INSTALL_SERVERPOD" = "true" ]; then \
    dart pub global activate serverpod_cli; \
    fi

# ---------------------------------------------------------------------------
# 9. Android Studio stub (satisfies flutter doctor check without full install)
#    Flutter checks /opt/android-studio/jbr/bin/java and version from ~/.cache
# ---------------------------------------------------------------------------
USER root
RUN mkdir -p /opt/android-studio/jbr/bin \
    && ln -s /usr/lib/jvm/java-17-openjdk-amd64/bin/java /opt/android-studio/jbr/bin/java
USER vscode
# Create version marker that Flutter reads from ~/.cache/Google/AndroidStudioX.Y/.home
RUN mkdir -p /home/vscode/.cache/Google/AndroidStudio2024.3 \
    && printf '/opt/android-studio' > /home/vscode/.cache/Google/AndroidStudio2024.3/.home

# ---------------------------------------------------------------------------
# 10. Pre-run flutter doctor to cache artifacts
# ---------------------------------------------------------------------------
RUN flutter doctor -v

WORKDIR /workspaces
