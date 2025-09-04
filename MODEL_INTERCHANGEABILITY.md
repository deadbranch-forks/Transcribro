### Claim
**Different ggml model sizes (tiny/base/small) are interchangeable within the current codebase.**
**Supported.**
- `MainRecognitionService` loads `ggml-model-whisper-tiny.en-q8_0.bin` via `WhisperContext.createContextFromAsset` without any branches for other model sizes.
- `WhisperRepository` lazily initializes a `WhisperContext` and performs transcription through model-agnostic APIs, so swapping the model only requires changing the file path.
- The `WhisperContext` wrapper provides generic functions such as `createContextFromAsset`, accepting an arbitrary asset path.
- Only one model asset (`ggml-model-whisper-tiny.en-q8_0.bin`) exists in `app/src/main/assets/models/whisper`, and no additional code handles alternative variants.

### Conclusion
The repository's inference pipeline is model-agnostic. Any ggml Whisper model (e.g., base or small) could replace the bundled tiny model simply by supplying a different ggml file.
