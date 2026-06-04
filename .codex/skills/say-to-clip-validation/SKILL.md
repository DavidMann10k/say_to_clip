---
name: say-to-clip-validation
description: Use when testing, smoke-testing, debugging, or diagnosing this repository's dictation workflow, including live provider checks, local Whisper/Ollama setup, Groq setup, logging, generated audio, clipboard behavior, and privacy-safe troubleshooting.
---

# say_to_clip Validation

The validation path can touch microphones, clipboard, local Ollama, local Whisper, or Groq. Treat it as live and privacy-sensitive.

## Default Validation Order

1. Read `README.md` and `CONTRIBUTING.md` if the requested behavior is unclear.
2. Run syntax checks after script edits:

```bash
bash -n say_to_clip ollama-manager smoke-test
```

3. Run ShellCheck when installed:

```bash
shellcheck say_to_clip ollama-manager smoke-test
```

4. Use `./smoke-test` for end-to-end validation only when the user wants live validation or the change affects provider/runtime behavior.

## Smoke Test Facts

- `./smoke-test` loads `~/.config/say_to_clip/env`.
- It generates silent test audio with `ffmpeg`; it does not record the microphone.
- It calls `./say_to_clip transcribe "$AUDIO_FILE"` with an isolated `DICTATE_WORKDIR`.
- It validates dependency presence, provider requirements, generated outputs, privacy cleanup, and that `wl-copy --foreground` is still running.
- It may call Groq if either transcription or cleanup is configured for Groq.
- It may call local Ollama and local Whisper when configured for local providers.

## Privacy-Safe Debugging

- Do not print `GROQ_API_KEY`, transcripts, cleaned text, raw provider responses, or API payloads unless the user explicitly asks and understands the sensitivity.
- Prefer reporting provider names, HTTP status, file existence, byte counts, character counts, and command exit statuses.
- Use `--log` for operational details. Use `--log-content` only for transcript/API payload debugging with explicit need.
- Keep temporary workdirs private and clean them up when they are no longer needed.
- Check `.gitignore` before adding any generated file pattern; secrets, logs, audio, transcripts, and cleaned text should not be committed.

## Targeted Diagnostics

- Local transcription issues: check `whisper`, `ffmpeg`, `DICTATE_WHISPER_MODEL`, `DICTATE_WHISPER_LANGUAGE`, and whether Whisper wrote the expected `.txt`.
- Local cleanup issues: check `DICTATE_OLLAMA_URL`, `./ollama-manager status`, and the `/api/tags` health endpoint.
- Groq issues: check whether `GROQ_API_KEY` is set without displaying it, then inspect HTTP status and redacted error messages.
- Clipboard issues: check `wl-copy`, the stored `wl-copy.pid`, and whether the foreground process is still alive.
- Quiet recordings: inspect `SAY_TO_CLIP_MIN_MEAN_VOLUME_DB`, selected `DICTATE_MIC_SOURCE`, and the `ffmpeg volumedetect` mean volume in logs.
