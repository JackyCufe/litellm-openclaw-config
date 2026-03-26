[![中文](https://img.shields.io/badge/README-中文-red)](README.zh.md)

# LiteLLM Config for OpenClaw

A minimal but production-ready [LiteLLM](https://github.com/BerriAI/litellm) proxy configuration for running Claude models with **Prompt Caching**, designed to work with [OpenClaw](https://github.com/openclaw/openclaw).

Two configs included — pick the one that fits you:

| Config | Who it's for | Prerequisite |
|--------|-------------|-------------|
| `litellm.config.anthropic.yaml` | **Individual / Personal** | Anthropic API Key only |
| `litellm.config.yaml` | **Enterprise / GCP users** | Google Cloud project + Vertex AI |

---

## 🙋 Personal Quick Start (Recommended)

### Prerequisites

1. **Anthropic API Key** — get one at [console.anthropic.com](https://console.anthropic.com)
2. **LiteLLM installed:** `pip install litellm[proxy]`

### Setup

```bash
git clone https://github.com/JackyCufe/litellm-openclaw-config
cd litellm-openclaw-config
export ANTHROPIC_API_KEY=sk-ant-YOUR_KEY_HERE
litellm --config litellm.config.anthropic.yaml --port 4000
```

Test it:

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-YOUR-MASTER-KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "claude-sonnet-4-6", "messages": [{"role": "user", "content": "Hello!"}]}'
```

### Using with OpenClaw

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

1. Google Cloud Project with Vertex AI API enabled
2. Service Account JSON key with `Vertex AI User` role
3. Claude model access in [Vertex AI Model Garden](https://console.cloud.google.com/vertex-ai/model-garden)

### Setup

```bash
cp litellm.config.yaml litellm.config.local.yaml
# Edit: YOUR_GCP_PROJECT_ID, /path/to/service-account.json, master_key
litellm --config litellm.config.local.yaml --port 4000
```

---

## 💡 Why LiteLLM + Prompt Caching?

Both configs enable **Prompt Caching** — cuts repeated token costs by up to **90%** for AI agents that inject large context files every turn (like OpenClaw's MEMORY.md, SOUL.md, etc.).

```yaml
cache_control_injection_points:
  - location: message
    role: system
  - location: message
    index: -1
```

---

## Files

| File | Purpose |
|------|---------|
| `litellm.config.anthropic.yaml` | Personal — Claude via Anthropic direct API |
| `litellm.config.yaml` | Enterprise — Claude + Gemini via GCP Vertex AI |

---

## Security Notes

- ⚠️ Never commit API keys or service account JSON
- Use env vars: `export ANTHROPIC_API_KEY=sk-ant-...`
- Add `litellm.config.local.yaml`, `*.json`, `.env` to `.gitignore`

---

## References

[LiteLLM Docs](https://docs.litellm.ai) · [Anthropic API](https://docs.anthropic.com) · [OpenClaw](https://github.com/openclaw/openclaw)

---

Maintained by [@JackyCufe](https://github.com/JackyCufe). PRs welcome!
