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
