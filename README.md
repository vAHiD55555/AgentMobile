# AgentMobile: AI Agent Platform for Android

<div align="center">


![License](https://img.shields.io/badge/License-MIT-blue.svg)
![API](https://img.shields.io/badge/API-28%2B-brightgreen.svg)
![Kotlin](https://img.shields.io/badge/Kotlin-2.0-7F52FF.svg?logo=kotlin&logoColor=white)
![Rust](https://img.shields.io/badge/Rust-stable-DEA584.svg?logo=rust&logoColor=white)

**Transform your Android device into a powerful AI agent host**

</div>

## Overview

AgentMobile is a native Android application that transforms your smartphone into a continuously running AI agent platform. Built with a Rust core and Kotlin/Compose UI, it provides secure, battery-optimized hosting for autonomous AI agents that can interact with 30+ AI providers.

### Key Features

- **Enterprise-Grade Security**: AES-256-GCM encrypted API key storage with hardware-backed keystore
- **Native Performance**: Rust-based engine compiled directly to ARM, no emulation layers
- **Battery Optimized**: Intelligent foreground service with 2-5% daily battery consumption
- **Universal Compatibility**: Support for OpenAI, Claude, Gemini, Ollama, and 25+ more providers
- **Zero Infrastructure**: No servers, cloud bills, or complex setup required
- **Modern UI**: Material Design 3 with dynamic theming and full accessibility support

## Quick Start

### Prerequisites
- Android 9.0 (API 28) or higher
- 2GB+ RAM recommended
- Internet connection for cloud providers

### First Steps

```bash
# For developers building from source
git clone --recursive https://github.com/shubham230523/AgentMobile.git
cd AgentMobile
./gradlew :app:assembleDebug
```

## Architecture

AgentMobile combines the best of native Android development with systems programming:

```
┌─────────────────────────────────────┐
│        Kotlin/Compose UI            │
│  Material Design 3 • Accessibility   │
└─────────────┬───────────────────────┘
              │ UniFFI Bridge
┌─────────────▼───────────────────────┐
│         Rust Core Engine             │
│    Tokio Runtime • Memory Safe      │
└─────────────────────────────────────┘
```

### Core Components

- **Frontend Layer**: Jetpack Compose UI with Material Design 3
- **Service Layer**: Android foreground service with lifecycle management
- **Bridge Layer**: Mozilla UniFFI for seamless Rust-Kotlin interoperability
- **Engine Layer**: ZeroClaw router with 34+ FFI functions

## Terminal Interface

AgentMobile includes a powerful terminal REPL for direct daemon interaction:

### Command Examples
```bash
/status                    # Check daemon health
/cost daily 2026 3 31     # View today's usage
/events 10                # Show recent activity
/cron add "0 */6 * * *" "cleanup"  # Schedule cleanup
/help                     # List all commands
```

### Advanced Features
- **Rhai Scripting**: Full scripting engine for automation
- **Image Support**: Vision model integration with file attachments
- **Command History**: Persistent history with search functionality
- **Accessibility**: Complete screen reader support

## Security Model

Your data security is our top priority:

### Encryption Architecture
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   API Keys      │───▶│ AES-256-GCM      │───▶│ Android Keystore│
│   (Plaintext)   │    │   Encryption     │    │  (Hardware)     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### Security Features
- **Hardware-Backed Storage**: StrongBox secure element on supported devices
- **Biometric Protection**: Fingerprint/face unlock for sensitive operations
- **Zero Cloud Exposure**: Keys never leave your device unencrypted
- **Memory Safety**: Rust prevents buffer overflows and use-after-free bugs
- **Reproducible Builds**: Every build can be verified from source code

## Provider Ecosystem

AgentMobile supports the widest range of AI providers in any mobile platform:

### Major Providers
| Provider | Type | Authentication |
|----------|------|----------------|
| OpenAI | GPT-4, DALL-E | API Key |
| Anthropic | Claude | API Key |
| Google | Gemini | API Key |
| Ollama | Local Models | URL Only |
| Groq | Ultra-Fast | API Key |

### Full List
<details>
<summary>View All 32+ Providers</summary>

| Provider | Auth Method | Category |
|----------|-------------|----------|
| OpenAI | API Key | Primary |
| Anthropic | API Key | Primary |
| Google Gemini | API Key | Primary |
| OpenRouter | API Key | Primary |
| Ollama | URL Only | Primary |
| LM Studio | URL + Key | Primary |
| vLLM | URL + Key | Primary |
| LocalAI | URL + Key | Primary |
| Groq | API Key | Ecosystem |
| Mistral | API Key | Ecosystem |
| xAI/Grok | API Key | Ecosystem |
| DeepSeek | API Key | Ecosystem |
| Together AI | API Key | Ecosystem |
| Fireworks AI | API Key | Ecosystem |
| Perplexity | API Key | Ecosystem |
| Cohere | API Key | Ecosystem |
| GitHub Copilot | API Key | Ecosystem |
| Amazon Bedrock | URL + Key | Ecosystem |
| Cloudflare AI | URL + Key | Ecosystem |
| Novita AI | API Key | Ecosystem |
| Telnyx | API Key | Ecosystem |
| Custom OpenAI | URL + Key | Custom |
| Custom Anthropic | URL + Key | Custom |

</details>

## Battery Optimization

AgentMobile is designed for continuous operation with minimal battery impact:

### Power Management
- **Smart Sleep**: Daemon sleeps when idle, wakes only for tasks
- **Adaptive Performance**: Reduces activity during battery saver mode
- **Network Awareness**: Optimizes for WiFi vs cellular connections
- **OEM Compatibility**: Handles manufacturer-specific battery optimizations

### Real-World Performance
- **Idle Consumption**: 2-5% battery per day
- **Active Usage**: 10-15% during heavy agent activity
- **Standby Time**: Weeks of continuous operation
- **Thermal Management**: No overheating or performance throttling

## Device Requirements

### Minimum Requirements
- **OS**: Android 9.0 (API 28)
- **RAM**: 2GB+
- **Storage**: 100MB free space
- **Network**: WiFi or cellular connection

### Recommended Setup
- **OS**: Android 12+ for Material You theming
- **RAM**: 4GB+ for multiple concurrent agents
- **Storage**: 500MB+ for logs and data
- **Network**: Stable WiFi for cloud providers

### Compatibility Notes
- **Root Not Required**: Works on stock Android installations
- **No Google Play**: Distributed via GitHub Releases
- **OEM Support**: Includes optimizations for Xiaomi, Samsung, Huawei

## Development

### Build Requirements
- **JDK**: 17+ (Eclipse Adoptium recommended)
- **Android SDK**: API 35
- **Android NDK**: r27c
- **Rust**: 1.85+ stable
- **cargo-ndk**: 4.x

### Build Commands
```bash
# Setup environment
export JAVA_HOME="/path/to/jdk-17"
export ANDROID_HOME="/path/to/Android/Sdk"

# Install Rust targets
rustup target add aarch64-linux-android x86_64-linux-android

# Build debug APK
./gradlew :app:assembleDebug

# Run tests
./gradlew :app:testDebugUnitTest :lib:testDebugUnitTest

# Code quality checks
./gradlew spotlessCheck detekt
```

### Project Structure
```
AgentMobile/
├── app/                    # Android application
├── lib/                    # Library module (AAR)
├── agentmobile/            # Rust workspace
│   └── zeroclaw-ffi/      # UniFFI bindings
├── zeroclaw/               # Upstream submodule
└── .github/workflows/      # CI/CD pipelines
```

## FAQ

<details>
<summary>Can I run local AI models on my phone?</summary>

AgentMobile acts as an intelligent router. For local inference, connect to an Ollama instance running on your network. The app handles orchestration while the heavy computation happens on your server or workstation.
</details>

<details>
<summary>How much data does it use?</summary>

Data usage varies by provider. Text-based conversations typically use 1-5MB per hour. Vision tasks and file operations use more. Local Ollama instances use only your local network.
</details>

<details>
<summary>Will this work when my phone is locked?</summary>

Yes! AgentMobile runs as a foreground service and continues operating when the screen is off, the app is backgrounded, or the device is locked.
</details>

<details>
<summary>Is my data private?</summary>

Absolutely. API keys are encrypted on-device and never transmitted unencrypted. All processing happens either locally (with Ollama) or on the provider's servers according to their privacy policies.
</details>

<details>
<summary>Can I run multiple agents?</summary>

Yes! Create multiple agents with different providers, models, and configurations. Each operates independently with its own memory and tools.
</details>
