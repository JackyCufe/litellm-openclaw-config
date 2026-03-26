# LiteLLM Config for OpenClaw

A minimal but production-ready [LiteLLM](https://github.com/BerriAI/litellm) proxy configuration for running Claude models with **Prompt Caching**, designed to work with [OpenClaw](https://github.com/openclaw/openclaw).

Two configs included — pick the one that fits you:

| Config | Who it's for | Prerequisite |
|--------|-------------|-------------|
| `litellm.config.anthropic.yaml` | **Individual / Personal** | Anthropic API Key only |
| `litellm.config.yaml` | **Enterprise / GCP users** | Google Cloud project + Vertex AI |

---

## 🙋 Personal Quick Start (Recommended)

The simplest path — just an Anthropic API Key and you're done.

### Prerequisites

1. **Anthropic API Key** — get one at [console.anthropic.com](https://console.anthropic.com)
2. **LiteLLM installed:**
   ```bash
   pip install litellm[proxy]
   ```

### Setup

```bash
# Clone this repo
git clone https://github.com/JackyCufe/litellm-openclaw-config
cd litellm-openclaw-config

# Set your API key
export ANTHROPIC_API_KEY=sk-ant-YOUR_KEY_HERE

# Start the proxy
litellm --config litellm.config.anthropic.yaml --port 4000
```

That's it. Test it:

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-YOUR-MASTER-KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-6",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### Using with OpenClaw

In your OpenClaw config, point to the local proxy:

```yaml
model: litellm/claude-sonnet-4-6
litellm:
  api_base: http://localhost:4000
  api_key: sk-YOUR-MASTER-KEY
```

---

## 🏢 Enterprise / GCP Vertex AI

For teams already on Google Cloud — Claude + Gemini via Vertex AI, unified GCP billing.

### Prerequisites

1. **Google Cloud Project** with Vertex AI API enabled
2. **Service Account** JSON key with `Vertex AI User` role
3. **Claude model access** requested in Vertex AI Model Garden ([apply here](https://console.cloud.google.com/vertex-ai/model-garden))
4. LiteLLM installed

### Setup

```bash
# Copy and fill in your values
cp litellm.config.yaml litellm.config.local.yaml
# Edit: YOUR_GCP_PROJECT_ID, /path/to/service-account.json, master_key, ui_password

# Start the proxy
litellm --config litellm.config.local.yaml --port 4000
```

---

## 💡 Why LiteLLM + Prompt Caching?

Both configs include **Prompt Caching** — the biggest cost saver when running AI agents:

```yaml
cache_control_injection_points:
  - location: message
    role: system     # Cache long system prompts (MEMORY.md, SOUL.md, etc.)
  - location: message
    index: -1        # Cache the last user message
```

With an agent like OpenClaw that injects large workspace files every turn, caching slashes repeated token costs by up to 90%.

### `drop_params` — Compatibility Fix

OpenClaw and some other clients send extra fields (`store`, `service_tier`) that Claude/Vertex AI doesn't accept. These settings silently drop them:

```yaml
drop_params: true
additional_drop_params:
  - store
  - service_tier
```

---

## Files

| File | Purpose |
|------|---------|
| `litellm.config.anthropic.yaml` | Personal config — Claude via Anthropic direct API |
| `litellm.config.yaml` | Enterprise config — Claude + Gemini via GCP Vertex AI |

---

## Security Notes

- ⚠️ Never commit your actual API keys or service account JSON
- Use environment variables for secrets:
  ```bash
  export ANTHROPIC_API_KEY=sk-ant-...
  export LITELLM_MASTER_KEY=sk-...
  ```
- Add `litellm.config.local.yaml`, `*.json`, `.env` to `.gitignore`

---

## References

- [LiteLLM Docs](https://docs.litellm.ai)
- [Anthropic API Docs](https://docs.anthropic.com)
- [LiteLLM Vertex AI Guide](https://docs.litellm.ai/docs/providers/vertex)
- [OpenClaw](https://github.com/openclaw/openclaw)

---

Maintained by [@JackyCufe](https://github.com/JackyCufe). PRs welcome!
