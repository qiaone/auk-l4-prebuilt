# audio.cpp (custom build: AuK only) for Google Colab L4 (sm_89, CUDA 13)

- Binaries built from 0xShug0/audio.cpp main commit 75d0294 (2026-10-08), deployment build (model specs embedded).
- `audiocpp_cli --version`: audio.cpp dev, git 75d0294, gcc 13.3.0, backends cpu,cuda.
- LICENSE: MIT (from audio.cpp upstream); model weights license: MIT (Tencent AuK).
- Model set: custom, only `auk` (Tencent AuK / AuK-Flash).
- Requires a Colab GPU runtime whose image ships CUDA 13 (`/usr/local/cuda-13.0`); it links
  libcublas.so.13 / libcudart.so.13 from the image.
- Model GGUF files are NOT bundled; download them from Hugging Face with Xet:

  export HF_XET_HIGH_PERFORMANCE=1
  hf download audio-cpp/AuK-Base-and-Flash-GGUF \
    auk-flash-f32.gguf auk-flash-f16.gguf auk-flash-q8_0.gguf \
    qwen2.5-omni-3b-q8_0.gguf auk-vae-f32.gguf \
    config/ tokenizer/ --local-dir models/AuK-Base-and-Flash-GGUF

- CLI example (zero-shot TTS):
  bin/audiocpp_cli --task tts --family auk \
    --model models/AuK-Base-and-Flash-GGUF/auk-flash-q8_0.gguf \
    --backend cuda --device 0 --threads 4 \
    --session-option auk.variant=flash \
    --session-option auk.model_gguf=auk-flash-q8_0.gguf \
    --session-option auk.qwen_gguf=qwen2.5-omni-3b-q8_0.gguf \
    --session-option auk.vae_gguf=auk-vae-f32.gguf \
    --voice-ref ref.wav \
    --text "Say the following with the same voice: 'hello'" \
    --request-option duration_sec=3 --out out.wav

- Batch: --request-sequence requests.json --out-dir segments
