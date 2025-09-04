## Whisper integration overview

- The app module depends on a native `:lib` module that builds the Whisper C++ library via CMake; the app adds this module as a Gradle dependency to expose JNI bindings in Kotlin

- Kotlin wrapper `WhisperContext` loads the appropriate native library at runtime and exposes `transcribeData`, which forwards audio buffers and thread counts to JNI functions implemented in `WhisperLib`

- The actual native entry points initialize a `whisper_context`, run `whisper_full` for inference, and return text segments back to Kotlin

- `MainRecognitionService` constructs a `WhisperRepository` that lazily loads a `WhisperContext` from an embedded model asset (`ggml-model-whisper-tiny.en-q8_0.bin`) using `WhisperContext.createContextFromAsset`

- `WhisperRepository.transcribeAudio` converts PCM samples to floats, pads short clips to 32\u202f000 samples, and calls `WhisperContext.transcribeData` to obtain transcripts, trimming a known hallucination suffix before returning the result

### Runtime flow
1. Audio is captured in `MainRecognitionService` and optionally segmented by VAD.
2. Each segment is passed to `WhisperRepository.transcribeAudio`, which ensures a `WhisperContext` is loaded and forwards the processed buffer to `transcribeData`.
3. `WhisperContext` delegates to JNI `fullTranscribe`, which runs the Whisper model and returns decoded text segments.
4. The repository aggregates segments and delivers final or partial transcription results back to the recognition service.

This setup cleanly layers the Whisper model: the native library handles inference, `WhisperContext` manages JNI interaction and threading, and the recognition service provides Android speech-recognition semantics around the model.

## Model source
### Known

- The app’s recognition service loads ggml-model-whisper-tiny.en-q8_0.bin from the asset path models/whisper/ggml-model-whisper-tiny.en-q8_0.bin, providing the model used at runtime

- A license note confirms that OpenAI Whisper models live under app/src/main/assets/models/whisper, establishing the repository location of bundled models

- The JNI layer compiles against ggml sources, including ggml-quants.c and ggml-cpu-quants.c, indicating reliance on ggml’s quantization routines from whisper.cpp

- Commit history shows a deliberate switch to the tiny.en-q8_0 model with a justification that q5_1 “isn’t much faster and may cause quality degradation,” highlighting the chosen quantization scheme and model size increase

- Commented code lists alternative quantization options (Q4_0, Q4K, Q2K) and notes that the k‑quant variants are “very slow and inaccurate,” reflecting awareness of other ggml formats
Inferred

- The ggml-model-whisper-tiny.en-q8_0.bin file name and ggml build configuration imply the model is a whisper.cpp ggml binary quantized with the q8_0 scheme (8‑bit per weight), likely derived from the English-only tiny Whisper model.

### Unknown

- The repository contains no scripts or documentation explaining how ggml-model-whisper-tiny.en-q8_0.bin was produced—whether it was downloaded pre-quantized or generated locally, and with what exact commands or parameters.

- The precise source and version of the base tiny.en model are unspecified beyond the generic OpenAI licensing note.

- Details of the quantization methodology (e.g., tool settings, data used, validation) and empirical performance comparisons with other quantizations remain undocumented.
