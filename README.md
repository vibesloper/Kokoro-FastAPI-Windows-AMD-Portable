# Kokoro-FastAPI-WINDWOS-AMD-Portable

# Kokoro-FastAPI — Portable Windows Build (AMD Radeon RX 9070 XT / RDNA 4)

A self-contained, zero-install Windows distribution of **Kokoro-FastAPI** with native **AMD ROCm GPU acceleration**, pre-bundled Python 3.12, `espeak-ng`, Kokoro-82M weights, and custom MIOpen audiobook optimizations (~41x real-time on an RX 9070 XT).

---

## Key Features & Performance Optimizations

* **100% Portable & Self-Contained:** Bundles embeddable Python 3.12, AMD ROCm PyTorch wheels (`gfx1201`), `espeak-ng`, model weights (`kokoro-v1_0.pth`), voice packs, and pre-compiled MIOpen kernels—no Python, Git, or Docker installation required.
* **64-Step Tensor Shape Bucketing (`kokoro_v1.py`):** Pads phoneme tokens (up to 512) and predicted audio frames to multiples of 64 so MIOpen compiles only a tiny set of fixed shapes rather than recompiling on every new sentence length.
* **Async Chunk Prefetching (`tts_service.py`):** Overlaps CPU text normalization and `espeak-ng` phonemization with GPU synthesis while preserving strict streaming order.
* **Audiobook Chunk Window (`440–495` tokens):** Packs chunks close to Kokoro's 510-token limit to minimize padding overhead and maximize GPU throughput.
* **Smart GPU Launcher (`start-portable.bat` + `gpu_scan.py`):**
  * Enumerates available GPUs, displays architecture (`gfxXXXX`) and VRAM, and defaults to your discrete Radeon RX GPU (bypassing Ryzen integrated graphics).
  * Automatically offers a one-time ROCm kernel download if run on a different AMD GPU architecture (such as RX 9060 `gfx1200` or RX 7000 `gfx1100`).
  * Defaults to **1 GPU worker** (benchmarked at **~41x real-time** on RX 9070 XT with ~1.5 GB VRAM usage), with an optional prompt to scale workers up on high-VRAM cards.

---

## System Requirements

* **OS:** Windows 10 or Windows 11 (64-bit) *(Windows-only; Linux users should use the official ROCm Docker container)*
* **GPU:** AMD Radeon RX 9070 / RX 9070 XT (`gfx1201`) out of the box, or other ROCm-supported Radeon RX GPUs via automatic first-run kernel download
* **Drivers:** Up-to-date **AMD Software: Adrenalin Edition** graphics drivers

---

## Quick Start

1. **Download & Extract:**
   * Download all release archive parts (`.7z` or `.7z.001`, `.7z.002`, etc.) into the same folder.
   * Right-click the first file (`.7z` or `.7z.001`) -> **7-Zip** -> **Extract Here** to a local drive (e.g., `C:\Kokoro-FastAPI`).
2. **Launch:**
   * Double-click **`start-portable.bat`**.
   * Select your discrete Radeon RX GPU if prompted, and press **Enter** to accept the default `1` GPU worker.
3. **Open the Web UI:**
   * Once the terminal displays `Uvicorn running on http://0.0.0`, open **[http://localhost:8880/web](http://localhost:8880/web)** in your browser.
   * *Tip:* Select **WAV** output format for fastest local generation without CPU MP3 encoding overhead.

### API Endpoints
* **OpenAI-Compatible Speech API:** `http://localhost:8880/v1/audio/speech`
* **Interactive API Docs:** `http://localhost:8880/docs`

---

## Folder Layout

```text
Kokoro-FastAPI/
├── python/               # Portable Python 3.12 + ROCm PyTorch & dependencies
├── espeak-ng/            # Portable eSpeak NG binaries, data & GPLv3 license
├── api/                  # Kokoro-FastAPI backend, models, voices & web UI
├── .miopen/              # Local MIOpen compiled kernel cache
├── gpu_scan.py           # ROCm GPU architecture & VRAM detection helper
├── start-portable.bat    # Interactive GPU launcher
├── README-PORTABLE.txt   # End-user quickstart guide
└── LICENSE               # Apache 2.0 License
```

---

## Licenses & Attribution

* **Kokoro-FastAPI:** Based on [remsky/Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) (Apache License 2.0).
* **Kokoro-82M Model & Voices:** Created by [hexgrad/Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M) (Apache License 2.0).
* **eSpeak NG:** Bundled unmodified from [espeak-ng/espeak-ng](https://github.com/espeak-ng/espeak-ng) (GNU GPLv3; see `espeak-ng/COPYING.txt`).
