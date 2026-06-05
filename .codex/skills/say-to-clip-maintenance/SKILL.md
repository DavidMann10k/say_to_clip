---
name: say-to-clip-maintenance
description: Use when editing or reviewing this repository's say_to_clip Bash dictation pipeline, provider configuration, privacy behavior, runtime files, transcript cleanup, clipboard handling, or README/brief guidance for those features.
---

# say_to_clip Maintenance

This repo is a DIY/reference Linux dictation project, not a packaged product. Keep changes plain, inspectable, and easy to adapt.

## Project Shape

- `say_to_clip` is the main Bash implementation.
- `say_to_clip.env.example` documents safe config defaults.
- `smoke-test` validates the live configured provider path.
- `README.md`, `CONTRIBUTING.md`, and `local-dictation-agent-brief.md` set the project framing.
- `ollama-manager` is only a Docker helper for local Ollama.

## Maintenance Rules

- Preserve the hotkey flow: `toggle` starts recording when idle and stops, transcribes, cleans, and copies when recording.
- Preserve explicit commands: `start`, `stop`, `toggle`, `status`, `transcribe FILE`, and `help`.
- Keep provider selection split-friendly: `DICTATE_PROVIDER`, `DICTATE_TRANSCRIBE_PROVIDER`, and `DICTATE_CLEAN_PROVIDER`.
- When adding config, update `CONFIG_ENV_NAMES`, defaults in `say_to_clip`, `usage`, and `say_to_clip.env.example` together.
- Preserve env precedence: command environment overrides survive loading `~/.config/say_to_clip/env`.
- Use `jq` for JSON payload construction/parsing; avoid hand-built JSON.
- Keep Groq API keys out of visible command arguments; use temporary curl config files or another non-leaky mechanism.
- Keep runtime files private: `umask 077`, `$XDG_RUNTIME_DIR` when available, per-user `/tmp` fallback, and `chmod 700` workdirs.
- Keep transcript/API payload logging opt-in. `SAY_TO_CLIP_LOG=1` is operational logging; `SAY_TO_CLIP_LOG_CONTENT=1` is sensitive content logging.
- When logging is off, successful runs should remove recordings, transcripts, cleaned output, and provider response temp files.
- Preserve notification lifecycle behavior: recording start creates a persistent red-light notification with live mic-level status when supported, recording stop dismisses it by ID, progress handoff notifications remain ordinary text notifications, and completion uses the green ready-light notification.
- Keep clipboard behavior compatible with Wayland: `wl-copy --foreground` stays alive and its pid is stored so later copies can replace it.
- Do not add product-style install machinery unless the user explicitly asks; this repo is meant as a reference springboard.

## Checks

Run syntax checks for script edits:

```bash
bash -n say_to_clip ollama-manager smoke-test
```

Run ShellCheck when available:

```bash
shellcheck say_to_clip ollama-manager smoke-test
```

Run `./smoke-test` only when a live provider validation is appropriate. It may call local Ollama/Whisper or Groq based on the user's config.
