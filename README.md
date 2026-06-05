# say_to_clip

##### A vibe-coding springboard for Linux dictation tools.

This is not a polished dictation product. It is a DIY reference project: a
working example, an agent brief, and a set of design notes for building your own
Linux speech-to-clipboard workflow with a local coding agent.

The included `say_to_clip` script is the version I built for my
Arch/Omarchy/Hyprland machine. Treat the code like a magazine project from the
hardware store: useful to study, useful to adapt, not something you are expected
to install unchanged.

The core idea:

```text
hotkey -> record mic -> transcribe speech -> clean transcript -> copy to clipboard
```

## Start Here

If you want to build your own version, hand this brief to your coding agent:

[`local-dictation-agent-brief.md`](local-dictation-agent-brief.md)

That brief is the main artifact. The code in this repo is one implementation of
the idea, not the canonical product.

## What To Build

A practical Linux dictation tool needs a few replaceable parts:

| Part | Example in this repo | What your version might use |
| --- | --- | --- |
| Global trigger | Hyprland hotkey calling `toggle` | Any desktop shortcut system |
| Recorder | `pw-record` / PipeWire | Another recorder for your audio stack |
| Transcription | local Whisper or Groq | Any local/API speech-to-text provider |
| Cleanup | local Ollama or Groq | Any local/API LLM, or no cleanup |
| Clipboard | `wl-copy` | X11, Wayland, or desktop-specific clipboard tooling |
| Feedback | terminal messages and notifications | Notifications, sounds, tray UI, logs |

The important design is not the exact tool choice. It is that every stage is
plain, inspectable, and replaceable.

## Reference Implementation

This repo includes a Bash implementation named `say_to_clip`. It demonstrates:

- `start`, `stop`, `toggle`, `status`, and `transcribe FILE` commands
- a hotkey-friendly toggle flow
- PipeWire microphone recording
- local Whisper transcription
- Groq API transcription
- local Ollama transcript cleanup
- Groq API transcript cleanup
- hybrid modes, such as Groq transcription with local Ollama cleanup
- optional cleanup bypass for raw transcripts
- Wayland clipboard copy through `wl-copy`
- default microphone support through PipeWire's default source
- persistent red recording notification and green ready notification when the
  notification daemon supports IDs/icons
- live microphone level feedback in the recording notification
- quiet-audio detection before transcription
- private runtime files under `$XDG_RUNTIME_DIR` or a per-user `/tmp` fallback
- config defaults in `~/.config/say_to_clip/env`
- command environment overrides for one-off experiments
- privacy-first logging defaults
- optional content logging when debugging
- a live smoke test for the currently configured provider path
- an optional Docker helper for local Ollama

Those are features worth considering in your own build. They are not a claim
that this exact script is the right fit for your machine.

## Local vs API

The example supports two broad shapes:

```text
local: pw-record -> Whisper -> Ollama -> wl-copy
api:   pw-record -> Groq transcription -> Groq cleanup -> wl-copy
```

The local path keeps audio and text on your machine. The API path can be faster and easier to run, but it
sends audio/transcripts to a provider. A good personal tool should make that
tradeoff explicit.

## Privacy Notes

Speech-to-text tools handle private material by default because they listen to
whatever you say. The reference implementation tries to keep the safer behavior
as the default:

- no transcript content is printed to the terminal by default
- no log file is written by default
- recordings and transcripts are removed after successful runs
- API response temp files are removed
- transcript/API payload logging requires an explicit debug flag
- Groq API keys are kept out of visible `curl` command arguments

Your own version should make equally deliberate choices. If you add richer logs,
history, a GUI, or cloud providers, revisit the privacy model.

## Files

```text
say_to_clip                         reference Bash implementation
say_to_clip.env.example             example config defaults
smoke-test                          live pipeline smoke test
ollama-manager                      optional Dockerized Ollama helper
local-dictation-agent-brief.md      prompt/brief for building your own version
```

## Developer Checks

There is no build system. If you change the example scripts, these are the basic
checks:

```bash
bash -n say_to_clip ollama-manager smoke-test
shellcheck say_to_clip ollama-manager smoke-test
```

The smoke test is live. It uses the configured provider path, so it may call
Groq or local Ollama/Whisper depending on your config:

```bash
./smoke-test
```

## AI Assistance

This project was written with AI coding assistance. That is part of the point:
small, personal Linux tools are now realistic things to build with an agent, as
long as you keep the system understandable and review what it generates.

Use this as a starting point, not authority.

## License

Licensed under the Apache License, Version 2.0.
