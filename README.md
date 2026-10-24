
<p align="center">
<img width="500" height="500" alt="imgupscaler-enhanced" src="https://github.com/user-attachments/assets/2372ef9c-60bb-4595-b7d9-8a6371f25b51" />




<div align="center">

  <h1>Zipiteer</h1>
  <p><strong>Eats the archive, keeps the files.</strong></p>
  <p>A zero-overhead, in-place archive extractor and packager designed for SSDs with tight disk space.</p>

  <p>
    <a href="#the-problem">The Problem</a> •
    <a href="#key-features">Features</a> •
    <a href="#peek-mode">Peek Mode</a> •
    <a href="#installation">Installation</a> •
    <a href="#safety-and-integrity">Safety</a> •
    <a href="[#license](https://github.com/abullabu/zipiteer/blob/main/LICENSE)">License</a>
  </p>

</div>

---


<img width="1198" height="799" alt="image" src="https://github.com/user-attachments/assets/edd8213b-d368-4e20-9c96-70f92e477ddb" />


## The Problem

Extracting a 70 GB game archive on an SSD with only 80 GB of free space always fails with traditional archivers. Tools like WinRAR or 7-Zip keep the archive intact until decompression is fully complete, effectively demanding double the storage capacity: the compressed archive plus the extracted files.

Zipiteer resolves this limitation by consuming the archive in place during extraction:
- It progressively truncates and hole-punches archive data as output files are verified on disk.
- For split sets (.z01, .z02, ...), it unlinks fully processed volumes sequentially.
- It eliminates duplicate disk consumption without sacrificing archive integrity.

---

## Key Features

- **In-Place Extraction:** Progressively reclaims storage through NTFS hole-punching and truncation once extracted data passes CRC verification.
- **Smart Pack:** Progressively deletes verified source files while building the archive to prevent space exhaustion.
- **Multi-Volume Support:** Automatically detects PKZIP split sequences (.z01 through .zip) and deletes completed volumes on the fly.
- **Peek Mode (Selective Unpack):** Inspect archive contents before extracting to uncheck bloated assets, foreign language packs, or cutscenes.
- **Journaled Crash Recovery:** State transitions are tracked on disk. If interrupted by an unexpected crash or power failure, running the tool against the remaining archive resumes the operation.
- **Windows Integration:** Adds non-intrusive shell options ("Extract In-Place with Zipiteer" and "Peek with Zipiteer") to the Windows context menu.
- **Minimalist Architecture:** Written in Go with a responsive, standalone desktop interface powered by Wails.

---

## Peek Mode

Launching an archive with the "Peek with Zipiteer" context action opens the inspection view before decompression starts:

- Browse through archive files and directories with relative paths and uncompressed sizes.
- Exclude unnecessary directories or files using the interface toggles.
- Excluded entries are bypassed in the stream without hitting the destination disk.
- Note: Because extraction is destructive to the archive, unchecked files are permanently discarded alongside the consumed source archive.

---

## Safety and Integrity

Zipiteer modifies archives in place, so data safety is built into the extraction loop:

- **Strict CRC-32 Verification:** Archive data is never truncated or deleted until the extracted output file has passed byte-level CRC verification.
- **Fail-Safe Abort:** If an archive entry is corrupted or fails integrity checks, extraction stops immediately to preserve the untouched portions of the archive.
- **Journaled State Machine:** If extraction is interrupted by a power cut or system crash, the internal journal identifies completed files so you can resume extraction without starting over.

---

## Installation

### Pre-built Installer
Download the latest `Zipiteer-Setup-1.0.0.exe` from the [Releases](https://github.com/abullabu/zipiteer/releases) page. The installer sets up the required binaries, desktop shortcuts, and shell context menu associations.

### Building from Source
Prerequisites: Go 1.22+, Node.js, and Wails v2.

```powershell
# Clone the repository
git clone [https://github.com/abullabu/zipiteer.git](https://github.com/abullabu/zipiteer.git)
cd zipiteer

# Compile CLI and GUI binaries
.\build.ps1

# Optional: Generate the Inno Setup installer package
.\build.ps1 -Installer
