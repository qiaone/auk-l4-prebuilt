# auk-l4-prebuilt

Prebuilt **audio.cpp** binaries for Google Colab **L4** (compute capability `sm_89`), built for the
`auk` model family only (Tencent AuK / AuK-Flash speech generation & editing).

## Why

Colab L4 has no official CUDA Linux prebuilt (the official Colab package is for T4/sm_75), and a
source build takes ~10 minutes. This repository lets a new L4 session skip compilation entirely.

## Contents

- `auk-l4-sm89-release/bin/audiocpp_cli` (72 MB) — CLI: TTS / editing / batch
- `auk-l4-sm89-release/bin/audiocpp_server` (80 MB) — HTTP server with embedded WebUI
- `auk-l4-sm89-release/model_specs/auk.json`
- `auk-l4-sm89-release/README.md`, `LICENSE`, `SHA256SUMS`
- `auk-l4-sm89-release.tar.gz` — single-archive convenience copy (~92 MB)

## Requirements

- Colab GPU runtime with NVIDIA **L4** (sm_89)
- Image ships CUDA 13 at `/usr/local/cuda-13.0` (binaries link `libcublas.so.13` / `libcudart.so.13`)
- Model GGUFs are **not** bundled. Download from Hugging Face with Xet:

```bash
export HF_XET_HIGH_PERFORMANCE=1
hf download audio-cpp/AuK-Base-and-Flash-GGUF \
  auk-flash-f32.gguf auk-flash-f16.gguf auk-flash-q8_0.gguf \
  qwen2.5-omni-3b-q8_0.gguf auk-vae-f32.gguf \
  config/ tokenizer/ --local-dir models/AuK-Base-and-Flash-GGUF
```

## Usage (zero-shot voice cloning)

```bash
bin/audiocpp_cli --task tts --family auk \
  --model models/AuK-Base-and-Flash-GGUF/auk-flash-q8_0.gguf \
  --backend cuda --device 0 --threads 4 \
  --session-option auk.variant=flash \
  --session-option auk.model_gguf=auk-flash-q8_0.gguf \
  --session-option auk.qwen_gguf=qwen2.5-omni-3b-q8_0.gguf \
  --session-option auk.vae_gguf=auk-vae-f32.gguf \
  --voice-ref ref.wav \
  --text "Say the following with the same voice: 'hello'" \
  --request-option duration_sec=3 --seed 42 --out out.wav
```

Batch mode: `--request-sequence requests.json --out-dir segments`

## Build provenance

- Source: [0xShug0/audio.cpp](https://github.com/0xShug0/audio.cpp) main commit `75d0294` (2026-10-08)
- Built on Colab L4: Ubuntu 24.04, gcc 13.3.0, CUDA 13.0, `--cuda-arch 89`
- CMake: `--model-set custom --models auk --deployment-build` (model specs embedded)
- `audiocpp_cli --version`: `audio.cpp dev, git 75d0294, backends: cpu,cuda`

## Licenses

- audio.cpp: MIT (`LICENSE` in bundle)
- Tencent AuK model weights: MIT — download them from Hugging Face, they are not part of this repository.
