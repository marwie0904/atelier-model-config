# atelier-model-config

Capability manifest for the Atelier desktop app. The app fetches
`manifest.json` from this repo's `main` branch at runtime
(`https://raw.githubusercontent.com/marwie0904/atelier-model-config/main/manifest.json`)
to populate its model menu and built-in slash-command catalog — so adding a
model or command is a one-file edit here, **not** an app release.

Edit `manifest.json` and push. The app re-fetches on startup, every 24h, on a
manual refresh, and when the local `claude` CLI version changes. If the fetch
fails (offline / first run) the app falls back to a bundled copy, so it always
works. Permission modes and reasoning/effort levels are read live from
`claude --help` and are **not** in this file.

## `manifest.json` schema

```jsonc
{
  "version": 1,                       // integer — BUMP on every change (compared for difference, so rollbacks propagate)
  "updated_at": "2026-06-20T00:00:00Z",
  "models": [
    {
      "id": "opus-4-8",               // stable internal id (the app's spawn key)
      "label": "Opus 4.8",            // text shown in the model menu
      "value": "opus",                // passed verbatim to `claude --model`
      "family": "opus",
      "contextWindow": 1000000,       // drives the context meter
      "pricing": { "input": 5, "output": 25 },  // optional, for the cost meter
      "default": true                 // optional; at most one
    }
  ],
  "commands": [
    { "name": "context", "description": "Show context-window usage", "kind": "command" }
  ]
}
```

### `value`: alias vs. pinned version

- An **alias** (`"value": "opus"`) always resolves to the newest model in that
  family — zero maintenance, auto-tracks new releases.
- A **full name** (`"value": "claude-opus-4-6"`) pins one exact version. Use
  this to offer an older model. Note: a pinned model only works if Anthropic
  still serves it for your subscription; if it's retired, selecting it errors.

To add a pinned older model, append an entry with its own `id` and the full
model name as `value`, then bump `version`.
