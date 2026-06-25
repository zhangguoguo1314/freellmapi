---
title: FreeLLMAPI
emoji: 🤖
colorFrom: blue
colorTo: purple
sdk: docker
app_port: 7860
pinned: false
license: mit
---

# FreeLLMAPI · 统一 LLM 路由器

**一个 OpenAI 兼容端点。十六个免费 LLM 提供商。每月约 17 亿 Token。**

FreeLLMAPI 将 Google、Groq、Cerebras、NVIDIA、Mistral、OpenRouter、GitHub Models、Cohere、Cloudflare、HuggingFace、Z.ai (智谱)、Ollama、Kilo、Pollinations、LLM7、OVH AI Endpoints 等免费层聚合到单一 `/v1/chat/completions` 端点之后。密钥使用加密存储。路由器为每个请求选择最佳可用模型，当某个提供商被限流时自动回退到下一个提供商，并跟踪每个密钥的使用量以确保不超过免费额度限制。

## 功能特点

- **OpenAI 兼容** — `POST /v1/chat/completions` 和 `GET /v1/models` 可与官方 OpenAI SDK 和任何 OpenAI 兼容客户端配合使用
- **Anthropic Messages API** — `POST /v1/messages` 支持 Claude Code 和 Anthropic SDK
- **图像生成和文本转语音** — `POST /v1/images/generations` 和 `POST /v1/audio/speech`
- **自动故障转移** — 当提供商返回 429/5xx 或超时时，自动跳过并重试下一个模型
- **每密钥速率跟踪** — 精细的 RPM/RPD/TPM/TPD 计数器
- **AES-256-GCM 加密密钥存储**
- **统一 API 密钥** — 使用单一 bearer token 进行认证
- **管理面板** — React + Vite UI，支持深色模式
- **分析仪表板** — 请求日志，延迟，Token 计数，成功率

## 快速开始

部署到 HuggingFace Spaces 后：

1. 打开 Space 页面，进入管理面板
2. 首次访问需要创建账号（设置邮箱和密码）
3. 在 **密钥** 页面添加你的提供商 API 密钥
4. 调整 **回退链** 顺序
5. 获取你的统一 API 密钥

### 使用 API

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://your-username-freellmapi.hf.space/v1",
    api_key="freellmapi-your-unified-key",
)

resp = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "你好！"}],
)
print(resp.choices[0].message.content)
```

## 配置

可以通过 HuggingFace Spaces 的 **Secrets and Variables** 功能设置环境变量：

| 变量名 | 必填 | 说明 |
| --- | --- | --- |
| `ENCRYPTION_KEY` | **是** | 64 字符十六进制密钥，用于加密 API 密钥存储（使用 `openssl rand -hex 32` 生成） |
| `PORT` | 否 | 默认 7860 |
| `PROXY_RATE_LIMIT_RPM` | 否 | 每 IP 代理请求限流（默认 120），设为 0 禁用 |
| `FREELLMAPI_CONTEXT_HANDOFF` | 否 | 模型切换时的上下文传递（`on_model_switch`） |

## 注意事项

- 数据存储在容器的 `/app/server/data` 目录中，Space 重启后数据可能丢失
- 建议将 `ENCRYPTION_KEY` 设置为 Space Secret 以确保安全
- 此为单用户代理，请勿暴露给互联网公开使用

## 许可证

[MIT](./LICENSE)

---

> ⚠️ 此项目汉化自 [FreeLLMAPI](https://github.com/tashfeenahmed/freellmapi)，由社区维护。
