### Claim 1
**The quantized ggml model used in this repo is the same as what would be used in whisper.cpp on the desktop.**
**Supported.**
- The recognition service loads `ggml-model-whisper-tiny.en-q8_0.bin`—a naming convention used by whisper.cpp for its ggml binaries

- Native build scripts compile whisper.cpp with ggml quantization sources such as `ggml-quants.c`, matching the desktop project’s build pipeline

- A commit explicitly switches from a q5_1 model to `tiny.en-q8_0`, noting quality concerns and keeping the standard ggml format used by whisper.cpp



### Claim 2
**The quantized ggml model used in this repo is in a format other than the format commonly used in whisper.cpp on desktop.**
**Rejected.**
- The only Whisper asset is `ggml-model-whisper-tiny.en-q8_0.bin`; commit history shows earlier use of other ggml variants (e.g., `ggml-base.en-q5_1.bin`), but all remain ggml files, the default format for whisper.cpp


- There is no evidence of alternate formats (e.g., gguf, TFLite, ONNX) for the Whisper model.

### Claim 3
**The quantized ggml model used in this repo (currently or past) is in a format known for optimized mobile inference, such as Q_8_8_0_0, TFLite, onnx, RTlite, etc.**
**Rejected.**
- The Whisper model assets are limited to ggml binaries; the only ONNX file in the repo is for Silero VAD, not Whisper, indicating no TFLite/ONNX/TFLite-like Whisper model is used now or historically



### Known
- The app’s recognition service loads `ggml-model-whisper-tiny.en-q8_0.bin` from the asset path `models/whisper/ggml-model-whisper-tiny.en-q8_0.bin`, providing the model used at runtime


- A license note confirms that OpenAI Whisper models live under `app/src/main/assets/models/whisper`, establishing the repository location of bundled models


- The JNI layer compiles against `ggml` sources, including `ggml-quants.c` and `ggml-cpu-quants.c`, indicating reliance on ggml’s quantization routines from `whisper.cpp`


- Commit history shows a deliberate switch to the `tiny.en-q8_0` model with a justification that `q5_1` “isn’t much faster and may cause quality degradation,” highlighting the chosen quantization scheme and model size increase


- Commented code lists alternative quantization options (`Q4_0`, `Q4K`, `Q2K`) and notes that the k‑quant variants are “very slow and inaccurate,” reflecting awareness of other ggml formats



### Inferred
- The `ggml-model-whisper-tiny.en-q8_0.bin` file name and ggml build configuration imply the model is a `whisper.cpp` ggml binary quantized with the `q8_0` scheme (8‑bit per weight), likely derived from the English-only `tiny` Whisper model.

### Unknown
- The repository contains no scripts or documentation explaining how `ggml-model-whisper-tiny.en-q8_0.bin` was produced—whether it was downloaded pre-quantized or generated locally, and with what exact commands or parameters.
- The precise source and version of the base `tiny.en` model are unspecified beyond the generic OpenAI licensing note.
- Details of the quantization methodology (e.g., tool settings, data used, validation) and empirical performance comparisons with other quantizations remain undocumented.
