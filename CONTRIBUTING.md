# Contributing

This is primarily a reference project, not an actively maintained product.

I do not expect contributions. The most useful thing to do with this repo is to
fork it, copy the agent brief, or use the scripts as starting material for your
own dictation workflow.

If you do open a change, keep the spirit of the project:

- preserve the DIY/reference framing
- keep the code plain and inspectable
- avoid adding product-style install machinery
- do not commit API keys, secrets, recordings, transcripts, or generated logs
- keep transcript/API payload logging opt-in

Basic checks for script changes:

```bash
bash -n say_to_clip ollama-manager smoke-test
shellcheck say_to_clip ollama-manager smoke-test
```

The smoke test is live and depends on your local config:

```bash
./smoke-test
```
