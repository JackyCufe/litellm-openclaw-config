# LiteLLM Config for OpenClaw

A minimal but production-ready [LiteLLM](https://github.com/BerriAI/litellm) proxy configuration for running Claude and Gemini models via **Google Cloud Vertex AI**, designed to work with [OpenClaw](https://github.com/openclaw/openclaw).

## What's Inside

| File | Purpose |
|------|---------|
| `litellm.config.yaml` | Main proxy config — model list, router settings, auth |

## Why Vertex AI for Claude?

Calling Claude through Vertex AI instead of the Anthropic API directly gives you:

- 🔐 **GCP IAM auth** — no API key rotation headaches
- 💰 **Potentially lower cost** depending on your GCP committed use discounts
- 🌏 **Regional control** — pin traffic to `us-east5` or other supported regions
- 🤝 **Unified billing** — Claude + Gemini on the same GCP invoice

## Prerequisites

1. **Google Cloud Project** with Vertex AI API enabled
2. **Service Account** JSON key with `Vertex AI User` role
3. **Claude model access** requested in Vertex AI Model Garden ([apply here](https://console.cloud.google.com/vertex-ai/model-garden))
4. **LiteLLM** installed:
   ```bash
   pip install litellm[proxy]
   ```

## Quick Start

```bash
# Clone this repo
git clone https://github.com/JackyCufe/litellm-openclaw-config
cd litellm-openclaw-config

# Copy and fill in your values
cp litellm.config.yaml litellm.config.local.yaml
# Edit: YOUR_GCP_PROJECT_ID, /path/to/service-account.json, master_key, ui_password

# Start the proxy
litellm --config litellm.config.local.yaml --port 4000
```

Then test it:

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-YOUR-MASTER-KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-6",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Key Config Highlights

### `drop_params: true` + `additional_drop_params`

OpenClaw (and some other clients) may send extra fields like `store` or `service_tier` that Vertex AI doesn't accept. These settings silently drop unknown params instead of erroring out.

```yaml
additional_drop_params:
  - store
  - service_tier
```

### `cache_control_injection_points`

Enables **prompt caching** on system messages and the last user turn automatically — cuts costs and latency on repeated context.

```yaml
cache_control_injection_points:
  - location: message
    role: system
  - location: message
    index: -1
```

### Adding OpenAI / Anthropic Direct

Uncomment the examples at the bottom of `litellm.config.yaml` and swap in your API keys.

## Using with OpenClaw

In your OpenClaw config, point the model provider to your local LiteLLM proxy:

```yaml
# openclaw config (example)
model: litellm/claude-sonnet-4-6
litellm:
  api_base: http://localhost:4000
  api_key: sk-YOUR-MASTER-KEY
```

## Security Notes

- ⚠️ Never commit your actual service account JSON or master key
- Add `litellm.config.local.yaml` and `*.json` to `.gitignore`
- Use environment variables for secrets in production:
  ```yaml
  vertex_credentials: os.environ/GOOGLE_APPLICATION_CREDENTIALS
  master_key: os.environ/LITELLM_MASTER_KEY
  ```

## .gitignore

```
litellm.config.local.yaml
*.json
.env
```

## References

- [LiteLLM Docs](https://docs.litellm.ai)
- [LiteLLM Vertex AI Guide](https://docs.litellm.ai/docs/providers/vertex)
- [OpenClaw](https://github.com/openclaw/openclaw)
- [Vertex AI Claude Model Garden](https://console.cloud.google.com/vertex-ai/model-garden)

---

Maintained by [@JackyCufe](https://github.com/JackyCufe). PRs welcome!
