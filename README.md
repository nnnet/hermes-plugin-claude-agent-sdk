# claude-agent-sdk

Hermes model-provider plugin that bridges Hermes to the **official
Anthropic Agent SDK** running on the host (via Claude Code subscription
auth, not API key).

## What it does

When Hermes selects `provider: claude-agent-sdk` for a model,
this plugin wraps the request in agent-CLI transport conventions
and routes it through the host Meridian proxy (or the SDK directly).
Auth is via the host's Claude Code subscription — no API key needed.

In the AiManager deployment this provider is the **fallback** for many
profiles (config.yaml `fallback_providers[0]`) — when primary
(`anthropic_custom` via CLR Gateway) fails, the bot falls through to
claude-agent-sdk before any other layer.

## Configuration

```yaml
providers:
  claude-agent-sdk:
    name: Claude Agent SDK
    base_url: http://127.0.0.1:3456     # host Meridian proxy
    api_key: not-needed                  # SDK handles auth via subscription
    api_mode: anthropic_messages
    default_model: claude-haiku-4-5
    discover_models: false
    models:
      - claude-opus-4-7
      - claude-sonnet-4-6
      - claude-haiku-4-5
```

## Mounting

Pulled by `sync-external-plugins.sh` into
`sources/hermes-external-plugins/claude-agent-sdk/`, bind-mounted at
`/opt/data/plugins/model-providers/claude-agent-sdk/`.

## Relationship to sibling plugins

- `claude-via-meridian` — plain HTTP Anthropic Messages, no CLI bridge
- `anthropic_custom` — Anthropic SDK against custom base_url (CLR Gateway)
- **`claude-agent-sdk`** — adds CLI-bridge semantics on top of Meridian

All three end up at the host Meridian proxy but with different
client-side framing.

## License

MIT — see LICENSE.
