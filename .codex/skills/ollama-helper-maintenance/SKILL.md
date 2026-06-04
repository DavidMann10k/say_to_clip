---
name: ollama-helper-maintenance
description: Use when editing, reviewing, or operating this repository's ollama-manager Docker helper, including container lifecycle commands, GPU backend selection, model pulls, health checks, environment overrides, and documentation for local Ollama support.
---

# Ollama Helper Maintenance

`ollama-manager` is an optional Docker-only helper for the reference dictation workflow. Keep it small and avoid turning it into a general installer.

## Command Surface

Preserve these commands unless the user asks for a breaking change:

```text
start [--cpu|--gpu|--rocm|--vulkan|--nvidia|--recreate]
stop
restart [start options]
status
logs
pull [MODEL]
choose
models
ps
remove
help
```

## Configuration

Supported environment overrides:

- `OLLAMA_CONTAINER_NAME`
- `OLLAMA_IMAGE`
- `OLLAMA_VOLUME`
- `OLLAMA_BIND`
- `OLLAMA_PORT`
- `OLLAMA_GPU=auto|never|rocm|vulkan|nvidia`
- `OLLAMA_DEFAULT_MODEL`

When adding config, update defaults, `usage`, and any docs together.

## GPU Behavior

- `never` means CPU.
- `rocm`/`amd`, `vulkan`, and `nvidia` force that backend.
- `auto` prefers ROCm when `/dev/kfd` and `/dev/dri` exist, then Vulkan when `/dev/dri` exists, then NVIDIA when the Docker runtime or NVIDIA device is present, then CPU.
- ROCm uses `ollama/ollama:rocm` when `OLLAMA_IMAGE` is not explicitly set.
- If GPU startup fails, the helper removes the failed container and retries CPU.

## Safety Rules

- `remove` removes the container only; model data remains in the Docker volume.
- Do not remove Docker volumes unless the user explicitly asks.
- Bind the API to `127.0.0.1` by default.
- Keep health checks simple: `status` uses `/api/tags` to report whether the API is responding.
- Avoid adding host package installation, service units, or desktop integration here unless explicitly requested.

## Checks

For script edits:

```bash
bash -n ollama-manager
shellcheck ollama-manager
```

For live operational checks, prefer non-destructive commands first:

```bash
./ollama-manager status
./ollama-manager models
./ollama-manager ps
```
