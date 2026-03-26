[![English](https://img.shields.io/badge/README-English-blue)](README.md)

# OpenClaw 专用 LiteLLM 配置

一份最小化但生产可用的 [LiteLLM](https://github.com/BerriAI/litellm) 代理配置，支持 Claude 模型 **Prompt 缓存**，专为 [OpenClaw](https://github.com/openclaw/openclaw) 设计。

提供两套配置，按需选择：

| 配置文件 | 适用人群 | 前提条件 |
|----------|---------|---------|
| `litellm.config.anthropic.yaml` | **个人用户（推荐）** | 仅需 Anthropic API Key |
| `litellm.config.yaml` | **企业 / GCP 用户** | 需要 Google Cloud 项目 + Vertex AI |

---

## 🙋 个人用户快速上手（推荐）

### 前提条件

1. **Anthropic API Key** — 在 [console.anthropic.com](https://console.anthropic.com) 申请
2. **安装 LiteLLM：** `pip install litellm[proxy]`

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
3. 在 [Vertex AI Model Garden](https://console.cloud.google.com/vertex-ai/model-garden) 申请 Claude 访问权限

### 启动

```bash
cp litellm.config.yaml litellm.config.local.yaml
# 填写：YOUR_GCP_PROJECT_ID、/path/to/service-account.json、master_key
litellm --config litellm.config.local.yaml --port 4000
```

---

## 💡 为什么用 LiteLLM + Prompt 缓存？

两套配置均内置 **Prompt Caching**——OpenClaw 每轮注入大量 workspace 文件（MEMORY.md、SOUL.md 等），缓存命中后重复 token 费用可降低 **90%**。

```yaml
cache_control_injection_points:
  - location: message
    role: system
  - location: message
    index: -1
```

---

## 文件说明

| 文件 | 用途 |
|------|------|
| `litellm.config.anthropic.yaml` | 个人版——Claude 直连 Anthropic API |
| `litellm.config.yaml` | 企业版——Claude + Gemini 走 GCP Vertex AI |

---

## 安全提示

- ⚠️ 切勿将 API Key 或 Service Account JSON 提交到 Git
- 使用环境变量：`export ANTHROPIC_API_KEY=sk-ant-...`
- 将 `litellm.config.local.yaml`、`*.json`、`.env` 加入 `.gitignore`

---

由 [@JackyCufe](https://github.com/JackyCufe) 维护，欢迎提 PR！
