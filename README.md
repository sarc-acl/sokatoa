# Sokatoa
A powerful GPU profiling platform for Android Vulkan applications

## What is Sokatoa?
Sokatoa is a new graphics tooling platform focused on providing developers better insights into their Vulkan applications’ runtime performance. This innovative tool offers integrated system-level profiling and frame-level profiling, providing developers with an in-depth, granular view of their applications’ overall performance.

### Key Features
-   **System-Level Profiling**: Comprehensive insights into CPU, GPU, and memory usage
-   **Frame-Level Analysis**: Precise analysis of Vulkan command streams, shader execution, and GPU tasks
-   **Shader Editing**: Modify and test shaders with live compilation feedback
-   **Replay Capabilities**: Replay profiles with modified shaders to see performance differences
-   **Comparison Tools**: Compare baselines with replays to analyze changes

## Quick Links
[Getting Started](docs/getting-started.md)
Learn how to install, configure, and use Sokatoa for GPU profiling.

[Release Notes](docs/release-notes.md)
View the latest changes and updates to Sokatoa.

## System Requirements
### Host Machine
-   ADB installed and on the path
-   macOS: macOS 14 or later (Apple silicon)
-   Linux: Ubuntu 22.04 or later
-   Windows: Windows 10 or later
-   Device connected to ADB (USB or Wi-Fi) - requires developer mode and USB debugging enabled for USB connection.
### Target Device
-   Android 13 or later
-   Debuggable APK or rooted device required for GFXR, Sokatoa Vulkan layers
-   For performance view data, Xclipse, Mali, Adreno or PowerVR GPU required

## About
Sokatoa is the next generation of Google’s Android GPU Inspector (AGI), built using modern web technologies (TypeScript/HTML/CSS/React) and the [Theia platform](https://theia-ide.org/). It is designed with extensibility at its core, allowing GPU vendors, third-party developers, and individual users to extend and enhance functionality through extensions.
