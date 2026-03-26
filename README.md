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
3. Claude model access requested in [Vertex AI Model Garden](https://console.cloud.google.com/vertex-ai/model-garden)

### Setup

```bash
cp litellm.config.yaml litellm.config.local.yaml
# Edit: YOUR_GCP_PROJECT_ID, /path/to/service-account.json, master_key
litellm --config litellm.config.local.yaml --port 4000
```

---

## 💡 Why LiteLLM + Prompt Caching?

Both configs enable **Prompt Caching** — the biggest cost saver for AI agents:

```yaml
cache_control_injection_points:
  - location: message
    role: system     # Cache long system prompts (MEMORY.md, SOUL.md, etc.)
  - location: message
    index: -1        # Cache the last user message
```

With OpenClaw injecting large workspace files every turn, caching cuts repeated token costs by up to **90%**.

---

## Files

| File | Purpose |
|------|---------|
| `litellm.config.anthropic.yaml` | Personal config — Claude via Anthropic direct API |
| `litellm.config.yaml` | Enterprise config — Claude + Gemini via GCP Vertex AI |

---

## Security Notes

- ⚠️ Never commit API keys or service account JSON
- Use environment variables: `export ANTHROPIC_API_KEY=sk-ant-...`
- Add `litellm.config.local.yaml`, `*.json`, `.env` to `.gitignore`

---

## References

- [LiteLLM Docs](https://docs.litellm.ai) · [Anthropic API](https://docs.anthropic.com) · [OpenClaw](https://github.com/openclaw/openclaw)

---

Maintained by [@JackyCufe](https://github.com/JackyCufe). PRs welcome!

---
---

# OpenClaw 专用 LiteLLM 配置

一份最小化但生产可用的 [LiteLLM](https://github.com/BerriAI/litellm) 代理配置，支持 Claude 模型 **Prompt 缓存**，专为 [OpenClaw](https://github.com/openclaw/openclaw) 设计。

提供两套配置，按需选择：

| 配置文件 | 适用人群 | 前提条件 |
|----------|---------|---------|
| `litellm.config.anthropic.yaml` | **个人用户（推荐）** | 仅需 Anthropic API Key |
| `litellm.config.yaml` | **企业 / GCP 用户** | 需要 Google Cloud 项目 + Vertex AI |

---

## 🙋 个人用户快速上手（推荐）

最简路径——只需一个 Anthropic API Key，5 分钟跑起来。

### 前提条件

1. **Anthropic API Key** — 在 [console.anthropic.com](https://console.anthropic.com) 申请
2. **安装 LiteLLM：**
   ```bash
   pip install litellm[proxy]
   ```

### 启动

```bash
git clone https://github.com/JackyCufe/litellm-openclaw-config
cd litellm-openclaw-config
export ANTHROPIC_API_KEY=sk-ant-YOUR_KEY_HERE
litellm --config litellm.config.anthropic.yaml --port 4000
```

验证：

```bash
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-YOUR-MASTER-KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "claude-sonnet-4-6", "messages": [{"role": "user", "content": "你好！"}]}'
```

### 在 OpenClaw 中使用

```yaml
model: litellm/claude-sonnet-4-6
litellm:
  api_base: http://localhost:4000
  api_key: sk-YOUR-MASTER-KEY
```

---

## 🏢 企业 / GCP Vertex AI 版

适合已在 Google Cloud 生态的团队——Claude + Gemini 统一走 Vertex AI，账单合并到 GCP。

### 前提条件

1. 已开通 Vertex AI API 的 GCP 项目
2. 具有 `Vertex AI User` 角色的 Service Account JSON 密钥
3. 在 [Vertex AI Model Garden](https://console.cloud.google.com/vertex-ai/model-garden) 申请 Claude 模型访问权限

### 启动

```bash
cp litellm.config.yaml litellm.config.local.yaml
# 填写：YOUR_GCP_PROJECT_ID、/path/to/service-account.json、master_key
litellm --config litellm.config.local.yaml --port 4000
```

---

## 💡 为什么用 LiteLLM + Prompt 缓存？

两套配置均内置 **Prompt Caching**，这是 AI Agent 场景下最有效的降本手段：

```yaml
cache_control_injection_points:
  - location: message
    role: system     # 缓存系统消息（MEMORY.md、SOUL.md 等长前缀）
  - location: message
    index: -1        # 缓存最后一条用户消息
```

OpenClaw 每轮都会注入大量 workspace 文件，缓存命中后重复 token 费用可降低 **90%**。

---

## 文件说明

| 文件 | 用途 |
|------|------|
| `litellm.config.anthropic.yaml` | 个人版——Claude 直连 Anthropic API |
| `litellm.config.yaml` | 企业版——Claude + Gemini 走 GCP Vertex AI |

---

## 安全提示

- ⚠️ 切勿将 API Key 或 Service Account JSON 提交到 Git
- 使用环境变量管理密钥：`export ANTHROPIC_API_KEY=sk-ant-...`
- 将 `litellm.config.local.yaml`、`*.json`、`.env` 加入 `.gitignore`

---

由 [@JackyCufe](https://github.com/JackyCufe) 维护，欢迎提 PR！
