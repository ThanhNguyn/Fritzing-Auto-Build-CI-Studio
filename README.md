# Automated Multi-OS Fritzing Builds (Linux, Windows, macOS)

[![Auto Check & Build Fritzing Multi-OS (Monthly)](https://github.com/ThanhNguyn/Fritzing-Auto-Build-CI-Studio/actions/workflows/build-fritzing.yml/badge.svg)](https://github.com/ThanhNguyn/Fritzing-Auto-Build-CI-Studio/actions/workflows/build-fritzing.yml)

Automated, unmodified builds of the open-source [Fritzing](https://github.com/fritzing/fritzing-app) electronics design platform and [fritzing-parts](https://github.com/fritzing/fritzing-parts) library, compiled directly from official upstream source code using GitHub Actions.

---

## How It Works

1. **Monthly Upstream Check (`0 3 1 * *`)**:
   - On the 1st of every month at 03:00 UTC, the `check-upstream` job queries the latest release tag/commit from `fritzing/fritzing-app`.
   - It checks whether a release for that exact version (`fritzing-<tag>-<short_sha>`) already exists in this repository's **GitHub Releases**.
   - If the version is already published, the workflow **skips all build jobs in ~15 seconds** to save runner minutes.
2. **Automated 3-OS Compilation**:
   - Whenever a new upstream version is detected (or when triggered manually with `force_build: true`), GitHub Actions compiles Fritzing across **Linux**, **Windows**, and **macOS** in parallel.
3. **Pre-Built SQLite Parts Database (`parts.db`)**:
   - Runs `Fritzing -db "../fritzing-parts/parts.db"` during packaging so the complete component library works immediately out of the box.

---

## Download Latest Builds

Visit the [**Releases**](../../releases/latest) page to download the latest packaged binaries for your operating system:

| Operating System | Architecture | Package Format | Description |
| :--- | :--- | :--- | :--- |
| **Linux** (Ubuntu 22.04+) | `x86_64` | `.AppImage` | Standalone executable (`chmod +x Fritzing-*.AppImage`) |
| **Windows** (10 / 11) | `x64` (MSVC) | `.zip` | Portable archive with all Qt6, libgit2, and ngspice DLLs |
| **macOS** (14+) | `arm64` (Apple Silicon) | `.dmg` | Ad-hoc signed application bundle (`Fritzing.app`) |

> **macOS Gatekeeper Note:** Because these community builds are ad-hoc signed without an Apple Developer ID certificate, run the following command if macOS shows an unverified developer prompt:
> ```bash
> xattr -cr /Applications/Fritzing.app
> ```

---

## C++ Dependency Stack

Fritzing's `phoenix.pro` build configuration expects exact sibling directories (`../`). The workflow automatically compiles and caches:

- **Qt SDK**: `6.5.3` (`qtbase`, `qtsvg`, `qtserialport`, `qt5compat`, `qtsql`)
- **libgit2**: `1.7.1` (`../libgit2/build64`)
- **ngspice**: `v42` shared simulation library (`../ngspice-42`)
- **Boost**: `1.83.0` header library (`../boost_1_83_0`)
- **QuaZip**: `1.4` built against Qt6 (`../quazip_qt6`)
- **PolyClipper**: `6.4.2` (`../clipper1`)
- **SVG++**: `1.3.1` (`../svgpp`)

---

## Repository Setup (If Forking or Creating From Scratch)

1. Place `.github/workflows/build-fritzing.yml` in your repository.
2. Go to **Settings → Actions → General → Workflow permissions** and select **Read and write permissions**, then click **Save**.
3. Open the **Actions** tab, select **Auto Check & Build Fritzing Multi-OS (Monthly)**, and click **Run workflow** for your initial build.

---

## Verifying Checksums

Every release includes a `SHA256SUMS.txt` file generated during CI. Verify your downloaded package with:

```bash
# Linux / macOS
sha256sum -c SHA256SUMS.txt --ignore-missing

# Windows PowerShell
Get-FileHash .\Fritzing-*-windows-x64.zip -Algorithm SHA256
```

---

## License

Fritzing source code is licensed under **GPL-3.0** by the [Fritzing organization](https://github.com/fritzing/fritzing-app).
