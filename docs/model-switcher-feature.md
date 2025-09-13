# Model Switcher Feature Proposal

## Summary
Users can select different Whisper GGML models or quantization levels from device storage via the GUI. This allows using models like tiny, base, small, large-v3 etc., enabling tradeoffs between accuracy and performance.

## Goals
- Provide UI for selecting model file from user storage.
- Persist selected model choice across app launches.
- Support listing known built-in models and discovered user-provided models.
- Allow quantization choice when multiple are available.
- Ensure fallback to default model if selection unavailable.

## Non-goals
- Downloading models from network.
- Automatic model updates.

## Design
### UI
- Add settings screen with 'Model' section listing available models.
- Provide file picker to import `.bin` ggml files from storage.
- Show model name and quantization (e.g., tiny-q5_1, base, large-v3).
- Option to select quantization from available types for built-in model.

### Data Flow
1. User selects model from settings; app copies or references file path.
2. `WhisperRepository` loads context using selected model file.
3. Selection is stored in shared preferences or database.
4. On startup, repository loads stored model; if missing, fallback to default.

### Model Management
- Store imported models in app-specific directory with metadata (name, size, quant).
- Provide removal option from UI.
- Validate model file before accepting.

## Implementation Steps
1. **Model metadata abstraction**: define data class capturing name, path, and quantization.
2. **Model storage**: utility to import and manage model files in app storage.
3. **Settings UI**: screen / dialog listing models and quantization options.
4. **Preference persistence**: save selected model identifier.
5. **Repository adaptation**: load model based on stored preference.
6. **Validation and error handling**: ensure file exists; show messages if invalid.

## Testing
- Unit tests for model metadata parsing and persistence.
- Instrumentation tests for settings UI interactions.
- Manual test instructions for importing and switching between models.

## Open Questions
- Should we bundle additional built-in models or rely solely on user-supplied files?
- How to handle model size limits and storage permissions?
