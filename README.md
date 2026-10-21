# Automated Multi-OS Fritzing Builds (Linux, Windows, macOS)

[![Auto Check & Build Fritzing Multi-OS (Monthly)](https://github.com/ThanhNguyn/Fritzing-Auto-Build-CI-Studio/actions/workflows/build-fritzing.yml/badge.svg)](https://github.com/ThanhNguyn/Fritzing-Auto-Build-CI-Studio/actions/workflows/build-fritzing.yml)

Automated, unmodified builds of the open-source [Fritzing](https://github.com/fritzing/fritzing-app) electronics design platform and [fritzing-parts](https://github.com/fritzing/fritzing-parts) library, compiled directly from official upstream source code using GitHub Actions.

---

## Key Features & Anti-Lag Optimizations
 
1. **Dynamic Upstream Dependency Detection (`pri/*detect.pri`)**:
   - Automatically parses `fritzing-app/src/version/version.cpp` and `fritzing-app/pri/*detect.pri` (`libgit2`, `QuaZip`, `Clipper1`, `Boost`, `SVG++`, `ngspice`) so sibling directory names and versions always match what `phoenix.pro` expects.
2. **Monthly Auto-Check & Deduplication (`0 3 1 * *`)**:
   - Checks `fritzing/fritzing-app@develop` on the 1st of every month at `03:00 UTC`.
   - If the current `<version>-<commit>` is already published in **GitHub Releases**, the workflow **skips all 3 OS compilation jobs in ~15 seconds**.
3. **Aggressive Compiler & Runtime Optimizations (Zero Canvas Lag)**:
   - **Link-Time Optimization (`LTO` / `/LTCG`)**: Enables cross-module inlining across `phoenix.pro`, `svgpp` template geometry parsers, and `Clipper1` copper fill routines.
   - **Native SIMD Vectorization**: Built with `/O2 /Ob3 /Oi /Ot /GL /fp:fast /arch:AVX2` on Windows (MSVC), `-O3 -flto=auto -ffast-math -march=x86-64-v3 -fomit-frame-pointer` on Linux (GCC), and `-O3 -flto=thin -ffast-math -fomit-frame-pointer` on macOS (Clang).
   - **Zero Debug Logging Overhead**: Defines `QT_NO_DEBUG`, `QT_NO_DEBUG_OUTPUT`, `QT_NO_INFO_OUTPUT`, `QT_NO_WARNING_OUTPUT`, and `NDEBUG` to eliminate runtime string formatting during breadboard/PCB dragging and zooming.
   - **Indexed & Defragmented `parts.db` + Packed `.git`**: Pre-builds `parts.db` at package time, runs SQLite `ANALYZE; VACUUM;` with 4 KB page alignment for instant component search, and compacts `fritzing-parts/.git` via `git gc --prune=now` for fast startup.
 
---
 
## Download Pre-Built Packages
 
Go to the [**Releases**](../../releases/latest) page to download the latest build for your operating system:
 
| Operating System | Architecture | Package Format | Bundled Runtime & Parts |
| :--- | :--- | :--- | :--- |
| **Linux** (Ubuntu 24.04+) | `x86_64` | `.tar.gz` (Self-Contained Portable) | Qt `6.8.3`, `libgit2`, `ngspice`, `QuaZip`, `Clipper1`, `parts.db` |
| **Windows** (10 / 11) | `x64` (MSVC 2022) | `.zip` (Self-Contained Portable) | Qt `6.8.3`, VC++ CRT, `ngspice.dll`, `quazip1-qt6.dll`, `parts.db` |
| **macOS** (14 / 15+) | `arm64` (Apple Silicon) | `.dmg` (Signed App Bundle) | Qt `6.8.2`, `libngspice.dylib`, `libquazip1-qt6.dylib`, `parts.db` |
 
---
 
## Repository Structure (Only 2 Files Needed)
 
```text
.
├── .github/
│   └── workflows/
│       └── build-fritzing.yml   # Automated monthly check + 3-OS LTO/AVX2 build & release pipeline
└── README.md                    # Repository documentation
```
 
---
 
## Setup Instructions
 
1. Copy `.github/workflows/build-fritzing.yml` and `README.md` into your GitHub repository.
2. Go to **Settings → Actions → General → Workflow permissions**, select **Read and write permissions**, and click **Save**.
3. Open the **Actions** tab, select **Auto Check & Build Fritzing Multi-OS (Monthly)**, click **Run workflow**, and click the green **Run workflow** button.
