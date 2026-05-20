# Linux Local Dictation Tool Agent Brief

Build me a Linux local dictation tool.

The workflow should be:

```text
global hotkey -> record microphone -> transcribe speech -> clean transcript with local LLM -> copy result to clipboard
```

Use this stack unless my system needs alternatives:

- Bash script
- PipeWire recording tool, such as `pw-record`
- OpenAI Whisper CLI or equivalent local Whisper package
- Ollama running locally, preferably in Docker
- Wayland clipboard tool, such as `wl-copy`
- Linux desktop global hotkey, such as Hyprland binds

The script should support:

```bash
start
stop
toggle
status
transcribe FILE
```

Use temp/debug files so I can inspect each stage:

```text
/tmp/local-dictation/recording.wav
/tmp/local-dictation/transcript.txt
/tmp/local-dictation/cleaned.txt
/tmp/local-dictation/dictation.log
```

Default to a fast English Whisper model like:

```text
base.en
```

Post-process with Ollama using a prompt like:

```text
Clean up this voice transcript without summarizing it.
Preserve every substantive word, sentence, exclamation, and non sequitur in order.
Remove only filler words like um, uh, and like when they do not change meaning.
Fix grammar, capitalization, and punctuation.
Do not answer questions in the transcript.
Output only the cleaned transcript.
```

Make the user flow:

```text
press hotkey -> speak -> press hotkey again -> cleaned text is on clipboard
```

Prioritize reliability and debug visibility over a GUI.

Print status messages. Send desktop notifications if available. Make every stage testable from the terminal.
