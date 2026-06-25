<div align="center">

# FreeLLMAPI

**一个 OpenAI 兼容端点。十六个免费 LLM 提供商。每月约 17 亿 Token。**

将 Google、Groq、Cerebras、NVIDIA、Mistral、OpenRouter、GitHub Models、Cohere、Cloudflare、HuggingFace、Z.ai (Zhipu)、Ollama、Kilo、Pollinations、LLM7、OVH AI Endpoints 以及 OpenCode Zen 的免费额度聚合在一起，加上任何自定义的 OpenAI 兼容端点（llama.cpp、LM Studio、vLLM、本地 Ollama），统一接入一个 `/v1/chat/completions` 端点。密钥以加密方式存储。路由器会为每个请求选择最佳可用模型，当某个提供商触发速率限制时自动切换到下一个，并跟踪每个密钥的使用量，确保你始终保持在每个免费额度的上限之下。

[![CI](https://github.com/tashfeenahmed/freellmapi/actions/workflows/ci.yml/badge.svg)](https://github.com/tashfeenahmed/freellmapi/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)
[![Docker image](https://img.shields.io/badge/ghcr.io-freellmapi-2496ED?logo=docker&logoColor=white)](https://github.com/tashfeenahmed/freellmapi/pkgs/container/freellmapi)

**[freellmapi.co](https://freellmapi.co)** — 浏览实时模型目录

![Fallback chain with per-provider token budget](repo-assets/fallback-chain.png)

</div>

---

## 目录

- [为什么会有这个项目](#为什么会有这个项目)
- [支持的提供商](#支持的提供商)
- [功能特性](#功能特性)
- [尚不支持的功能](#尚不支持的功能)
- [快速开始](#快速开始)
- [Docker](#docker)
- [桌面应用](#桌面应用)
- [语言](#语言)
- [Premium（实时目录）](#premium实时目录)
- [使用 API](#使用-api)
- [截图](#截图)
- [工作原理](#工作原理)
- [上下文交接](#上下文交接)
- [局限性](#局限性)
- [贡献](#贡献)
- [服务条款审查](#服务条款审查)
- [免责声明](#免责声明)

## 为什么会有这个项目

现在每个正经的 AI 实验室都提供免费额度——每月几百万 Token，每天几千次请求。单独来看每个额度都只是个玩具，但把它们叠加在一起，就相当于每月大约 **17 亿 Token** 的实际推理能力，涵盖 100 多个模型，从小型高速到具有相当能力的模型应有尽有。

问题在于手动叠加它们非常痛苦：十七种不同的 SDK、十七种不同的速率限制、十七个可能导致请求失败的地方。FreeLLMAPI 将这一切压缩成一个 OpenAI 兼容端点。只需将任何 OpenAI 客户端库指向你的本地服务器，它就能在你添加了密钥的提供商之间透明地路由。

## 支持的提供商

<table>
<tr>
<td align="center" width="180"><a href="https://ai.google.dev"><b>Google</b><br/>Gemini 2.5 Flash · 3.x previews</a></td>
<td align="center" width="180"><a href="https://groq.com"><b>Groq</b><br/>Llama 3.3, Llama 4, GPT-OSS, Qwen3</a></td>
<td align="center" width="180"><a href="https://cerebras.ai"><b>Cerebras</b><br/>Qwen3 235B</a></td>
<td align="center" width="180"><a href="https://opencode.ai/zen"><b>OpenCode Zen</b><br/>DeepSeek V4 Flash · Nemotron (promo)</a></td>
</tr>
<tr>
<td align="center"><a href="https://mistral.ai"><b>Mistral</b><br/>Large 3 · Medium 3.5 · Codestral · Devstral</a></td>
<td align="center"><a href="https://openrouter.ai"><b>OpenRouter</b><br/>21 free-tier models</a></td>
<td align="center"><a href="https://github.com/marketplace/models"><b>GitHub Models</b><br/>GPT-4.1 · GPT-4o</a></td>
<td align="center"><a href="https://developers.cloudflare.com/workers-ai"><b>Cloudflare</b><br/>Kimi K2 · GLM-4.7 · GPT-OSS · Granite 4</a></td>
</tr>
<tr>
<td align="center"><a href="https://cohere.com"><b>Cohere</b><br/>Command R+ · Command-A (trial)</a></td>
<td align="center"><a href="https://docs.z.ai"><b>Z.ai (Zhipu)</b><br/>GLM-4.5 · GLM-4.7 Flash</a></td>
<td align="center"><a href="https://build.nvidia.com"><b>NVIDIA</b><br/>NIM · 40 RPM free (eval-only ToS)</a></td>
<td align="center"><a href="https://huggingface.co/docs/inference-providers"><b>HuggingFace</b><br/>Router → DeepSeek V4 · Kimi K2.6 · Qwen3</a></td>
</tr>
<tr>
<td align="center"><a href="https://ollama.com"><b>Ollama Cloud</b><br/>GLM-4.7 · Kimi K2 · gpt-oss · Qwen3</a></td>
<td align="center"><a href="https://kilo.ai"><b>Kilo Gateway</b><br/>:free routes (anon ok)</a></td>
<td align="center"><a href="https://pollinations.ai"><b>Pollinations</b><br/>GPT-OSS 20B (anon ok)</a></td>
<td align="center"><a href="https://llm7.io"><b>LLM7</b><br/>GPT-OSS · Llama 3.1 · GLM (anon ok)</a></td>
</tr>
<tr>
<td align="center"><a href="https://endpoints.ai.cloud.ovh.net"><b>OVH AI Endpoints</b><br/>Qwen3.5 397B · GPT-OSS · Llama 3.3 (anon ok)</a></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
</tr>
</table>

外加一个**自定义**提供商——可以在 Keys 页面指向任何 OpenAI 兼容端点（llama.cpp、LM Studio、vLLM、本地 Ollama 或远程网关）。

## 功能特性

- **OpenAI 兼容** — `POST /v1/chat/completions` 和 `GET /v1/models` 可与官方 OpenAI SDK 及任何 OpenAI 兼容客户端（LangChain、LlamaIndex、Continue、Hermes 等）配合使用。只需更改 `base_url` 即可。
- **Responses API** — `POST /v1/responses`（当前 Codex CLI 版本所需的线协议格式）作为转换适配层实现在同一路由器上，支持完整的流式事件和工具调用。
- **Anthropic Messages API** — `POST /v1/messages`（以及 `/v1/messages/count_tokens`）使用 Anthropic 的线协议格式通过同一路由器，因此 **Claude Code** 和官方 Anthropic SDK 可以运行在你的免费模型池上。`GET /v1/models` 支持内容协商（当客户端发送 `anthropic-version` 时返回 Anthropic 格式，否则返回 OpenAI 格式），Claude 系列模型（`opus` / `sonnet` / `haiku` / `default`）映射到 `auto` 或你在 Keys 页面指定的模型。参见 [Anthropic / Claude 客户端](#anthropic--claude-客户端)。
- **图像生成和文本转语音** — `POST /v1/images/generations` 和 `POST /v1/audio/speech` 在支持媒体模型的提供商之间路由。可以在仪表盘的 **Models → Image / Audio** 标签页浏览和切换。
- **流式和非流式** — `stream: true` 时使用 Server-Sent Events，否则返回 JSON 响应。每个提供商适配器都实现了这两种模式。
- **工具调用** — OpenAI 风格的 `tools` / `tool_choice` 请求会透传，assistant 的 `tool_calls` 和 `tool` 角色的后续消息可以在提供商之间完整往返。
- **Embeddings** — `/v1/embeddings` 采用基于模型族的智能路由：故障切换只会在提供*相同*模型的提供商之间进行（不同模型的向量不兼容），永远不会跨模型切换。参见 [Embeddings](#embeddings)。
- **自动故障切换** — 如果选定的提供商返回 429、5xx 或超时，路由器会跳过它，将密钥置于短暂冷却期，然后在回退链中的下一个模型上重试（最多 20 次尝试）。
- **每密钥速率跟踪** — 按 `(平台, 模型, 密钥)` 维度的 RPM、RPD、TPM 和 TPD 计数器，确保路由器始终选择未超过限制的密钥。
- **粘性会话** — 多轮对话会在 30 分钟内保持使用同一模型，以避免对话中途切换模型导致的幻觉激增。
- **密钥加密存储** — API 密钥在写入 SQLite 前使用 AES-256-GCM 加密；解密在请求发送前的内存中完成。
- **统一 API 密钥** — 客户端使用单个 `freellmapi-…` bearer token 向你的代理进行身份验证。你永远不需要向上游应用暴露提供商密钥。
- **仪表盘登录** — 管理 UI 和所有 `/api/*` 路由都受邮箱 + 密码账户保护（scrypt 哈希、session token 认证），在首次运行时设置。`/v1` 代理保持自己独立的统一密钥认证供应用使用。
- **健康检查** — 定期探测将密钥标记为 `healthy`、`rate_limited`、`invalid` 或 `error`，使路由器自动跳过不可用的密钥。
- **管理仪表盘** — React + Vite UI，用于管理密钥、调整回退链顺序、查看分析数据和在 Playground 中运行提示词。包含暗色模式。
- **数据分析** — 按请求记录延迟、Token 计数、成功率和按提供商的细分统计。
- **模型切换时的上下文交接** — 可选功能。当会话回退到不同模型时，注入一条紧凑的系统消息，让新模型知道它正在继续一个已有任务。默认禁用；通过 `FREELLMAPI_CONTEXT_HANDOFF=on_model_switch` 启用。参见 [上下文交接](#上下文交接)。
- **在任何 Node 20+ 可运行的环境上运行** — Windows、macOS、Linux 服务器或小型 ARM SBC（包括 Raspberry Pi）。空闲时 RSS 内存约 40 MB，可运行在 PM2 / systemd / 任何你偏好的进程管理器下。

## 尚不支持的功能

功能范围刻意收窄。如果某个功能不在此列表中，也不在下方列出，则 assume 暂时不存在。

- **旧版 completions**（`/v1/completions`）— 仅实现了 chat 端点
- **内容审核**（`/v1/moderations`）
- **`n > 1`**（单次请求返回多个结果）
- **按用户计费 / 多租户认证** — 设计上为单用户

欢迎提交 PR 来添加以上任何功能。参见 [贡献](#贡献)。

## 快速开始

**一行命令**（需要 Docker — 设置 `~/freellmapi`，生成加密密钥，拉取镜像并启动容器）：

```bash
curl -fsSL https://freellmapi.co/install.sh | bash
```

更倾向于先阅读脚本内容再通过管道执行？[脚本在这里](https://freellmapi.co/install.sh)。重复运行是安全的：你的 `.env`（和加密密钥）会被保留，容器会更新到 `:latest`。可以通过 `FREELLMAPI_DIR`、`PORT` 或 `HOST_BIND` 环境变量覆盖默认值。

在 Windows 上，最简单的方式是从 Releases 下载桌面 **[`.exe` 安装包](https://github.com/tashfeenahmed/freellmapi/releases/latest)**（见下文）；Docker 方式可在 WSL 或任何 bash shell 中运行。

**或使用 Docker Compose 手动安装。** 它会在 3001 端口同时运行 API 和仪表盘，并将 SQLite 持久化到命名卷中。

**前置条件：** Docker、Docker Compose、OpenSSL。

```bash
git clone https://github.com/tashfeenahmed/freellmapi.git
cd freellmapi

# Generate an encryption key for at-rest key storage
ENCRYPTION_KEY="$(openssl rand -hex 32)"
printf "ENCRYPTION_KEY=%s\nPORT=3001\n" "$ENCRYPTION_KEY" > .env

docker compose up -d
```

打开 http://localhost:3001，在 **Keys** 页面添加你的提供商密钥，按需调整 **Fallback Chain** 顺序，然后从 **Keys** 页面顶部获取你的统一 API 密钥。这个统一密钥就是你要指向 OpenAI SDK 的密钥。

> **从其他机器访问？** 默认情况下容器仅绑定到 `127.0.0.1`，所以 `http://<server-ip>:3001` 无法从其他设备加载（页面只会挂起）。要在局域网中暴露它——例如 Raspberry Pi 上的 `http://192.168.1.x:3001`——使用 `HOST_BIND=0.0.0.0` 启动：
>
> ```bash
> HOST_BIND=0.0.0.0 docker compose up -d
> ```
>
> 仅在受信任的网络中执行此操作：代理是单用户的，仅受统一 API 密钥保护。

### 本地开发

**前置条件：** Node.js 20+、npm。

```bash
git clone https://github.com/tashfeenahmed/freellmapi.git
cd freellmapi
npm install
cp .env.example .env
ENCRYPTION_KEY="$(node -e 'console.log(require("crypto").randomBytes(32).toString("hex"))')"
printf "ENCRYPTION_KEY=%s\nPORT=3001\n" "$ENCRYPTION_KEY" > .env
npm run dev
```

`ENCRYPTION_KEY` 是启动的必需项。服务器仅在 `DEV_MODE=true` 且 `NODE_ENV` 不是 `production` 时回退到数据库存储的开发密钥；请不要使用该回退方式处理真实的提供商密钥。

请求分析数据默认保留 90 天或 100000 条请求记录，以先达到限制为准。在 `.env` 中设置 `REQUEST_ANALYTICS_RETENTION_DAYS=0` 或 `REQUEST_ANALYTICS_MAX_ROWS=0` 可以禁用对应的保留限制。

打开 http://localhost:5173（Vite 开发 UI），在 **Keys** 页面添加你的提供商密钥，按需调整 **Fallback Chain** 顺序，然后从 **Keys** 页面顶部获取你的统一 API 密钥。这个统一密钥就是你要指向 OpenAI SDK 的密钥。

> **从局域网内的其他设备访问开发 UI？** 使用 `npm run dev:lan` — 它会将 `--host` 传递给 Vite，Vite 会打印一个 `Network: http://<your-ip>:5173` URL，你可以在手机或其他机器上打开。（直接使用 `npm run dev -- --host` 在这里*不*起作用：根 `dev` 脚本是一个 `concurrently` 包装器，所以该标志不会传递到 Vite。）API 请求通过 Vite 的开发代理转发，无需额外配置服务器。

无需 Docker 的生产构建：

```bash
npm run build
node server/dist/index.js     # server + dashboard both served on :3001
```

## Docker

FreeLLMAPI 发布了一个包含 Express 服务器和构建完成的 React 仪表盘的单体生产镜像：

```bash
docker pull ghcr.io/tashfeenahmed/freellmapi:latest   # or pin a release, e.g. :v1.2.3
```

该镜像是多架构的（`linux/amd64` + `linux/arm64`，可在 Raspberry Pi 上运行）。发布的标签：`latest`（默认分支）、`v*.*.*`（git release 标签）和 `sha-<commit>`。

包含的 `docker-compose.yml` 是推荐的安装方式：

```bash
docker compose up -d
docker compose logs -f freellmapi
```

默认情况下容器端口绑定到 `127.0.0.1`（仅本地主机）。要从网络上的其他机器访问仪表盘/API，使用 `HOST_BIND=0.0.0.0 docker compose up -d` 发布到所有接口——仅在受信任的局域网中使用，因为代理是单用户的。

SQLite 数据存储在 `freellmapi-data` 卷的 `/app/server/data` 路径下。升级时保持相同的 `.env` `ENCRYPTION_KEY` 和卷，因为提供商密钥是加密静态存储的。

更多 Docker 操作和示例见 [docker/README.md](./docker/README.md)。

## HuggingFace Spaces 部署

FreeLLMAPI 可以部署到 HuggingFace Spaces，使用 Docker SDK。项目提供了专用的 `Dockerfile.hf` 和 `huggingface/` 目录中的 Space README。

**部署步骤：**

1. 在 HuggingFace 上创建一个新的 Docker Space
2. 将 `Dockerfile.hf` 复制为 `Dockerfile`（或直接上传）
3. 将 `huggingface/README.md` 的内容复制为 Space 的 `README.md`（包含 YAML frontmatter）
4. 上传项目所有源文件
5. 在 Space 的 **Settings → Variables and secrets** 中设置 `ENCRYPTION_KEY`（使用 `openssl rand -hex 32` 生成）
6. 等待 Space 构建完成，访问 Space URL 即可使用

> **注意：** HuggingFace Spaces 的存储是临时的，Space 重启后数据可能丢失。建议仅用于演示和测试。生产环境请使用 Docker 自行部署。

## 桌面应用

一个原生菜单栏应用位于 [`desktop/`](./desktop)：整个路由器 + 仪表盘从系统托盘本地运行，带有显示实时请求统计的毛玻璃弹窗。

![FreeLLMAPI desktop app](repo-assets/desktop.png)

**[从 Releases 下载](https://github.com/tashfeenahmed/freellmapi/releases/latest)** — macOS `.dmg` 和 Windows `.exe` 安装包由 [`desktop-release`](.github/workflows/desktop-release.yml) 工作流构建并附加到每个 release。也可以从本仓库在几分钟内构建：

```bash
npm install
npm run desktop:dist        # macOS  → desktop/dist-electron/FreeLLMAPI-…-arm64.dmg
npm run desktop:dist:win    # Windows → "desktop/dist-electron/FreeLLMAPI Setup ….exe"
```

> 本地构建的应用未签名，因此 Windows SmartScreen 可能会在首次运行时发出警告
>（"更多信息" → "仍然运行"）；macOS 构建可以在没有 Gatekeeper 提示的情况下启动。

## 语言

仪表盘和桌面托盘支持 6 种语言。UI 在首次加载时自动检测浏览器/系统语言，你可以随时从 **⋯** 菜单切换；选择会被记住。

| 语言 | Locale |
| --- | --- |
| English | `en` |
| 中文 (简体) | `zh-CN` |
| Français | `fr` |
| Español | `es` |
| Português (Brasil) | `pt-BR` |
| Italiano | `it` |

翻译文件位于 [`client/src/i18n/locales/`](./client/src/i18n/locales)，为扁平 JSON 文件。要添加新语言，复制 `en.json`，翻译值，然后在 `client/src/i18n/I18nProvider.tsx`（以及 `desktop/src/i18n.ts` 用于托盘字符串）中注册 locale——欢迎提交 PR。

## Premium（实时目录）

路由器会自动保持模型目录的最新状态：每天两次从 [freellmapi.co](https://freellmapi.co) 拉取签名目录，并将新模型、配额变更和提供商特性修复应用到你的本地数据库（你自己的启用/禁用选择和自定义提供商永远不会被修改；每次下载都会在应用前使用固定的 Ed25519 密钥进行验证）。

- **免费** 安装跟随**每月快照** — 永久免费。
- **[Premium](https://freellmapi.co/#pricing)**（$19/年 或 $49 终身）跟随**实时订阅**，每 2-3 天刷新一次，因此新免费模型出现的时刻就会出现在你的路由器中。一个密钥覆盖你的所有设备；在仪表盘的 **Premium** 部分激活。可在 [freellmapi.co/manage](https://freellmapi.co/manage) 自助取消或管理账单。

目录服务器永远不会看到你的提示词、补全结果或提供商密钥——无论哪种方式，路由器都是完全自托管的。

本地构建的应用可以在没有 Gatekeeper/SmartScreen 警告的情况下启动——不涉及代码签名。完整说明见 [desktop/README.md](./desktop/README.md)。

## 使用 API

任何 OpenAI 兼容客户端都可以使用（Anthropic / Claude 客户端也可以——参见 [Anthropic / Claude 客户端](#anthropic--claude-客户端)）。示例：

**Python**

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:3001/v1",
    api_key="freellmapi-your-unified-key",
)

resp = client.chat.completions.create(
    model="auto",  # let the router pick; or specify e.g. "gemini-2.5-flash"
    messages=[{"role": "user", "content": "Summarise the fall of Rome in one sentence."}],
)
print(resp.choices[0].message.content)
print("Routed via:", resp.headers.get("x-routed-via"))
```

**curl**

```bash
curl http://localhost:3001/v1/chat/completions \
  -H "Authorization: Bearer freellmapi-your-unified-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "auto",
    "messages": [{"role": "user", "content": "hi"}]
  }'
```

**流式传输**

```python
stream = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Stream me a haiku about SQLite."}],
    stream=True,
)
for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="", flush=True)
```

**工具调用**

传递 OpenAI 风格的 `tools` 和 `tool_choice`；助手响应通过代理完整往返，与 OpenAI API 完全一致。多步流程（assistant `tool_calls` → `tool` 角色后续 → 最终回答）在路由器可达的每个提供商上都能正常工作。

```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current weather for a city.",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}},
            "required": ["city"],
        },
    },
}]

# 1. Model asks for a tool call
first = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "What's the weather in Karachi?"}],
    tools=tools,
    tool_choice="required",
)
call = first.choices[0].message.tool_calls[0]

# 2. You execute the tool, feed the result back
final = client.chat.completions.create(
    model="auto",
    messages=[
        {"role": "user", "content": "What's the weather in Karachi?"},
        first.choices[0].message,
        {"role": "tool", "tool_call_id": call.id, "content": '{"temp_c": 32, "cond": "sunny"}'},
    ],
    tools=tools,
)
print(final.choices[0].message.content)
```

**Gemini Google Search grounding**

Google 的模型可以利用实时 Google 搜索结果来增强回答。由于 OpenAI 线协议格式无法表达这一点，请求一个名为 `google_search` 的工具，Google 提供商会将其转换为 Gemini 原生的 grounding 工具。它可以单独发送，也可以与你的普通 function tools 一起发送。

```python
resp = client.chat.completions.create(
    model="gemini-2.5-flash",  # pin a Google model so the request routes there
    messages=[{"role": "user", "content": "Who won the F1 race this weekend?"}],
    tools=[{"type": "function", "function": {"name": "google_search", "parameters": {}}}],
)
print(resp.choices[0].message.content)
```

**视觉 / 图像输入**

使用标准的 OpenAI `image_url` 内容块发送图像（base64 `data:` URL 或 `http(s)` URL）。当请求包含图像时，路由器会自动限制为**支持视觉的模型**并忽略纯文本模型。视觉模型在 Fallback Chain 页面上标有 **Vision** 徽章；当前集合包括 Gemini（2.5 / 3.x）、Llama 4 Scout/Maverick（Groq、NVIDIA）、GLM-4.6V Flash（Z.ai）、Nemotron Nano 12B VL（OpenRouter）和 GitHub 的 GPT-4o / GPT-4.1。

```python
resp = client.chat.completions.create(
    model="auto",  # auto-routes to a vision model
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "What's in this image?"},
            {"type": "image_url", "image_url": {"url": "data:image/png;base64,<...>"}},
        ],
    }],
)
print(resp.choices[0].message.content)
```

如果你的 Fallback Chain 中没有启用支持视觉的模型，图像请求会返回清晰的 `422`（`code: "no_vision_model"`）而不是静默丢弃图像。（`/v1/responses` 上的图像输入尚不支持——请使用 `/v1/chat/completions`。）

同样支持 `stream=True` — 你会收到 `delta.tool_calls` 块，最后以 `finish_reason: "tool_calls"` 结束。底层实现中，OpenAI 兼容提供商（Groq、Cerebras、Mistral、OpenRouter、GitHub Models、HuggingFace、Cloudflare、Cohere compat）的请求直接透传；Gemini 请求会被转换为 Google 的 `functionDeclarations` / `functionResponse` 格式，响应再转换回来。

每个响应都带有 `X-Routed-Via: <platform>/<model>` 头，让你可以看到实际是哪个提供商处理了每个调用。如果请求在提供商之间发生了回退，你还会看到 `X-Fallback-Attempts: N`。

### Embeddings

`/v1/embeddings` 与 OpenAI 兼容，但与 chat 路由有一个刻意的区别：**故障切换不会跨模型。** 不同模型的向量存在于不兼容的空间中——静默切换模型会破坏构建在代理之上的任何向量存储。因此 embedding 路由按**模型族**进行（一个模型标识 + 维度），故障切换只在提供同一模型族的提供商之间进行。

```python
resp = client.embeddings.create(
    model="auto",          # default family; or a family name like "bge-m3"
    input=["the quick brown fox", "pack my box with five dozen liquor jugs"],
)
print(len(resp.data), "vectors of", len(resp.data[0].embedding), "dims")
```

```bash
curl http://localhost:3001/v1/embeddings \
  -H "Authorization: Bearer freellmapi-your-unified-key" \
  -H "Content-Type: application/json" \
  -d '{"model": "auto", "input": "hello world"}'
```

`model` 接受 `auto`（配置的默认族）、族名称或提供商特定的模型 ID（会解析为其对应的族）。可用模型族：

| 模型族（`model`） | 维度 | 提供商（回退顺序） |
| --- | --- | --- |
| `gemini-embedding-001` *（默认）* | 3072 | Google |
| `text-embedding-3-large` | 3072 | GitHub Models |
| `text-embedding-3-small` | 1536 | GitHub Models |
| `embed-v4.0` | 1536 | Cohere |
| `bge-m3` | 1024 | Cloudflare → Hugging Face |
| `qwen3-embedding-0.6b` | 1024 | Cloudflare |
| `nv-embedqa-e5-v5` | 1024 | NVIDIA |
| `llama-nemotron-embed-1b-v2` | 2048 | NVIDIA |
| `llama-nemotron-embed-vl-1b-v2` | 2048 | NVIDIA → OpenRouter |
| `embeddinggemma-300m` | 768 | Cloudflare |

默认模型族、每个提供商的开关和优先级设置在仪表盘的 **Models → Embeddings** 页面。为给定的向量存储选择一个模型族并坚持使用——这正是模型族路由的全部意义所在。

### Anthropic / Claude 客户端

FreeLLMAPI 也支持 Anthropic 的 Messages API，因此任何为 Claude 构建的工具——包括 **Claude Code** 和官方 Anthropic SDK——都可以运行在你的免费模型池上。将客户端指向你服务器的**源地址**（Anthropic 客户端会自动附加 `/v1/messages`），使用你的统一密钥进行身份验证。同时支持 `x-api-key` 和 `Authorization: Bearer`。

```bash
curl http://localhost:3001/v1/messages \
  -H "x-api-key: freellmapi-your-unified-key" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 256,
    "messages": [{"role": "user", "content": "hi"}]
  }'
```

Claude 模型名称在 **Keys → Anthropic** 标签页映射到你的免费模型池：每个系列（`default`、`opus`、`sonnet`、`haiku`）路由到 `auto`（路由器选择免费模型）或你固定的模型。`POST /v1/messages/count_tokens` 和内容协商的 `GET /v1/models`（发送 `anthropic-version` 时返回 Anthropic 格式）也已实现。流式传输、系统提示词、工具使用和图像输入都通过同一路由器转换，与 OpenAI 端点一致。

**Claude Code** — 指向你的服务器并启动：

```bash
export ANTHROPIC_BASE_URL=http://localhost:3001
export ANTHROPIC_AUTH_TOKEN=freellmapi-your-unified-key   # NOT ANTHROPIC_API_KEY
claude
```

> 使用 `ANTHROPIC_AUTH_TOKEN`（作为 Bearer token 发送），**不要**使用 `ANTHROPIC_API_KEY` — Claude Code 会将已设置的 `ANTHROPIC_API_KEY` 视为冲突的官方凭据并拒绝启动。

## 截图

### Keys 页面

管理提供商凭据并获取应用连接所用的统一 API 密钥。每个密钥显示状态指示点和上次健康检查时间。

![Keys page](repo-assets/keys.png)

### Playground 页面

通过路由器发送聊天补全请求，查看是哪个提供商处理的，模型 ID 和延迟直接显示在消息上。

![Playground page](repo-assets/playground.png)

### Analytics 页面

请求量、成功率、输入输出 Token 数、平均延迟以及按提供商的细分统计，支持 24 小时 / 7 天 / 30 天时间窗口。

![Analytics page](repo-assets/analytics.png)

## 工作原理

```
┌──────────────────┐   Bearer freellmapi-…   ┌─────────────────────────┐
│  OpenAI SDK /    │ ──────────────────────▶ │  Express proxy (:3001)  │
│  curl / any      │ ◀────────────────────── │  /v1/chat/completions   │
│  OpenAI client   │      streamed tokens    └────────────┬────────────┘
└──────────────────┘                                      │
                                                          ▼
                             ┌────────────────────────────────────────────────┐
                             │  Router                                        │
                             │   1. Pick highest-priority model that          │
                             │      (a) has a healthy key and                 │
                             │      (b) is under all its rate limits.         │
                             │   2. Decrypt key, call provider SDK.           │
                             │   3. On 429/5xx → cooldown + retry next model. │
                             └────────────────────────────────────────────────┘
                                          │
   ┌──────────────┬────────────┬──────────┴─────────┬─────────────┬──────────┐
   ▼              ▼            ▼                    ▼             ▼          ▼
 Google         Groq        Cerebras           OpenRouter        HF       …10 more
```

- **路由器**（`server/src/services/router.ts`）— 为每个请求选择模型。
- **速率限制账本**（`server/src/services/ratelimit.ts`）— 基于 SQLite 的内存 RPM/RPD/TPM/TPD 计数器，在 429 时触发冷却。
- **提供商适配器**（`server/src/providers/*.ts`）— 每个提供商一个文件，实现 `Provider` 基类：`chatCompletion()` 和 `streamChatCompletion()`。
- **健康服务**（`server/src/services/health.ts`）— 定期探测以保持密钥状态最新。
- **仪表盘**（`client/`）— React + Vite + shadcn/ui 管理界面。
- **存储** — SQLite（`better-sqlite3`），使用 AES-256-GCM 信封加密存储密钥。

## 上下文交接

当 FreeLLMAPI 在对话中途回退到不同模型时（配额、速率限制、冷却期），新模型不知道它正在接手别人的任务。**上下文交接** 向出站请求添加一条紧凑的 `system` 消息，告诉新模型这一点：

```
FreeLLMAPI context handoff:
You are taking over an ongoing conversation from another model (groq:llama-3 → google:gemini-flash).
Continue the user's task using the conversation context already provided in this request.
Do not restart the task, re-ask already answered setup questions, or discard prior tool results.
Respect the user's latest message as the highest-priority instruction.

Recent session summary:
User: …
Assistant: …
```

**在 `.env` 中启用：**

```env
FREELLMAPI_CONTEXT_HANDOFF=on_model_switch
```

**工作原理：**

- 每个会话的消息存储在内存中（TTL：3 小时）。
- 仅在给定会话密钥所选模型发生变化时注入。
- 在首次请求、同模型继续或已存在交接消息时不注入。
- 会话密钥：如果存在 `X-Session-Id` 头则使用它，否则使用首条用户消息的 SHA-1 哈希（与粘性会话相同）。
- 存储仅在内存中。不会写入磁盘或记录日志。

> **重要提示：** 上下文交接可以改善通过 FreeLLMAPI 路由的对话的连续性。它无法恢复提供商内部的隐藏状态或从未发送到代理的消息。

## 局限性

叠加免费额度有其真实的取舍。请对它们保持清醒的认识：

- **没有前沿模型。** 免费目录的最高端模型大约是 Llama 3.3 70B、GLM-4.5、Qwen 3 Coder 和 Gemini 2.5 Pro。你无法通过此项目获得 GPT-5 或 Claude Opus 级别的推理能力。对于困难问题，请使用付费 API。
- **随着一天推进，智能水平会下降。** 你排名靠前的模型（通常是 Gemini 2.5 Pro、GitHub Models 的 GPT-4o）每日配额最低。一旦达到限制，路由器会沿优先级链向下回退到更小/更弱的模型。预计端点的有效智能水平会在每天的深夜时段下降——然后在 UTC 午夜重置。
- **延迟差异很大。** Cerebras 和 Groq 非常快；其他则不然。你只能使用当前可用的。
- **免费额度可能随时变更。** 提供商会定期收紧、放宽或移除免费额度。发生这种情况时，你会看到 429 或认证错误，直到更新目录。重新生成脚本位于 `server/src/scripts/`。
- **没有 SLA，这是定义上的。** 如果你需要可靠性，请使用有合同保障的付费提供商。
- **本地优先。** 没有多租户认证。为自己运行；不要暴露到互联网上。

## 贡献

非常欢迎贡献者！参见 [CONTRIBUTING.md](CONTRIBUTING.md) 了解开发流程、PR 期望以及关于 AI/LLM 辅助贡献的策略（简短版本：欢迎，与其他 PR 质量标准一致）。适合首次贡献的 PR：

- **添加一个提供商** — 复制 `server/src/providers/openai-compat.ts` 作为模板，接入 `server/src/providers/index.ts`，在 `server/src/db/index.ts` 中生成模型种子数据，在 `server/src/__tests__/providers/` 中添加测试。
- **添加一个端点** — 内容审核、旧版 completions。提供商基类可以扩展新方法；适配器声明它们支持哪些方法。
- **改进路由器** — 成本感知路由（最便宜-健康-最快的权衡）、更好的延迟加权优先级、区域固定。
- **仪表盘优化** — Analytics 页面的图表、密钥轮换 UX、从 `.env` 批量导入密钥。
- **文档** — 更多示例、Go/Rust 等语言的客户端库代码片段、Docker 或 Fly 的部署指南。

**开发流程：**

```bash
npm install
npm run dev      # server on :3001, dashboard on :5173, both with HMR
npm test         # server vitest; also runs client tests if the workspace adds them
npm run build    # compile server and dashboard
```

PR 应包含测试、保持现有测试套件通过、并符合仓库中已有的 `.editorconfig` / tsconfig 默认配置。Issues 和 Discussions 已开放。

### 贡献者

<a href="https://github.com/moaaz12-web"><img src="https://images.weserv.nl/?url=github.com/moaaz12-web.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@moaaz12-web" /></a>
<a href="https://github.com/lukasulc"><img src="https://images.weserv.nl/?url=github.com/lukasulc.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@lukasulc" /></a>
<a href="https://github.com/VinhPhamAI"><img src="https://images.weserv.nl/?url=github.com/VinhPhamAI.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@VinhPhamAI" /></a>
<a href="https://github.com/deadc"><img src="https://images.weserv.nl/?url=github.com/deadc.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@deadc" /></a>
<a href="https://github.com/zhangyu1324"><img src="https://images.weserv.nl/?url=github.com/zhangyu1324.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@zhangyu1324" /></a>
<a href="https://github.com/Tazrif-Raim"><img src="https://images.weserv.nl/?url=github.com/Tazrif-Raim.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Tazrif-Raim" /></a>
<a href="https://github.com/hodlmybeer69-bit"><img src="https://images.weserv.nl/?url=github.com/hodlmybeer69-bit.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@hodlmybeer69-bit" /></a>
<a href="https://github.com/phoenixikkifullstack"><img src="https://images.weserv.nl/?url=github.com/phoenixikkifullstack.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@phoenixikkifullstack" /></a>
<a href="https://github.com/jtbrennan-git"><img src="https://images.weserv.nl/?url=github.com/jtbrennan-git.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@jtbrennan-git" /></a>
<a href="https://github.com/praveenkumarpranjal"><img src="https://images.weserv.nl/?url=github.com/praveenkumarpranjal.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@praveenkumarpranjal" /></a>
<a href="https://github.com/nordbyte"><img src="https://images.weserv.nl/?url=github.com/nordbyte.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@nordbyte" /></a>
<a href="https://github.com/mybropro"><img src="https://images.weserv.nl/?url=github.com/mybropro.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@mybropro" /></a>
<a href="https://github.com/danscMax"><img src="https://images.weserv.nl/?url=github.com/danscMax.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@danscMax" /></a>
<a href="https://github.com/jhash"><img src="https://images.weserv.nl/?url=github.com/jhash.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@jhash" /></a>
<a href="https://github.com/JammyJames1234"><img src="https://images.weserv.nl/?url=github.com/JammyJames1234.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@JammyJames1234" /></a>
<a href="https://github.com/Sumit4codes"><img src="https://images.weserv.nl/?url=github.com/Sumit4codes.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Sumit4codes" /></a>
<a href="https://github.com/meliani"><img src="https://images.weserv.nl/?url=github.com/meliani.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@meliani" /></a>
<a href="https://github.com/thedavidweng"><img src="https://images.weserv.nl/?url=github.com/thedavidweng.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@thedavidweng" /></a>
<a href="https://github.com/bharvey42"><img src="https://images.weserv.nl/?url=github.com/bharvey42.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@bharvey42" /></a>
<a href="https://github.com/yuvrxj-afk"><img src="https://images.weserv.nl/?url=github.com/yuvrxj-afk.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@yuvrxj-afk" /></a>
<a href="https://github.com/Tushar49"><img src="https://images.weserv.nl/?url=github.com/Tushar49.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Tushar49" /></a>
<a href="https://github.com/nicyoong"><img src="https://images.weserv.nl/?url=github.com/nicyoong.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@nicyoong" /></a>
<a href="https://github.com/Aldo-f"><img src="https://images.weserv.nl/?url=github.com/Aldo-f.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Aldo-f" /></a>
<a href="https://github.com/Tazrif-Raim"><img src="https://images.weserv.nl/?url=github.com/Tazrif-Raim.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Tazrif-Raim" /></a>
<a href="https://github.com/m1nuzz"><img src="https://images.weserv.nl/?url=github.com/m1nuzz.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@m1nuzz" /></a>
<a href="https://github.com/LoneRifle"><img src="https://images.weserv.nl/?url=github.com/LoneRifle.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@LoneRifle" /></a>
<a href="https://github.com/ita333"><img src="https://images.weserv.nl/?url=github.com/ita333.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@ita333" /></a>
<a href="https://github.com/barbotkonv"><img src="https://images.weserv.nl/?url=github.com/barbotkonv.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@barbotkonv" /></a>
<a href="https://github.com/Naster17"><img src="https://images.weserv.nl/?url=github.com/Naster17.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Naster17" /></a>
<a href="https://github.com/StealthTensor"><img src="https://images.weserv.nl/?url=github.com/StealthTensor.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@StealthTensor" /></a>
<a href="https://github.com/EmranAhmed"><img src="https://images.weserv.nl/?url=github.com/EmranAhmed.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@EmranAhmed" /></a>
<a href="https://github.com/itsfuad"><img src="https://images.weserv.nl/?url=github.com/itsfuad.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@itsfuad" /></a>
<a href="https://github.com/RobinHoodO"><img src="https://images.weserv.nl/?url=github.com/RobinHoodO.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@RobinHoodO" /></a>
<a href="https://github.com/hmm183"><img src="https://images.weserv.nl/?url=github.com/hmm183.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@hmm183" /></a>
<a href="https://github.com/duemilionidieuro-bot"><img src="https://images.weserv.nl/?url=github.com/duemilionidieuro-bot.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@duemilionidieuro-bot" /></a>
<a href="https://github.com/hjhhoni"><img src="https://images.weserv.nl/?url=github.com/hjhhoni.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@hjhhoni" /></a>
<a href="https://github.com/immanuelsavio"><img src="https://images.weserv.nl/?url=github.com/immanuelsavio.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@immanuelsavio" /></a>
<a href="https://github.com/Slyker"><img src="https://images.weserv.nl/?url=github.com/Slyker.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Slyker" /></a>
<a href="https://github.com/wells1013"><img src="https://images.weserv.nl/?url=github.com/wells1013.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@wells1013" /></a>
<a href="https://github.com/evgkrsk"><img src="https://images.weserv.nl/?url=github.com/evgkrsk.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@evgkrsk" /></a>
<a href="https://github.com/aaronjmars"><img src="https://images.weserv.nl/?url=github.com/aaronjmars.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@aaronjmars" /></a>
<a href="https://github.com/Robs87"><img src="https://images.weserv.nl/?url=github.com/Robs87.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@Robs87" /></a>
<a href="https://github.com/dashitongzhi"><img src="https://images.weserv.nl/?url=github.com/dashitongzhi.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@dashitongzhi" /></a>
<a href="https://github.com/QingJ01"><img src="https://images.weserv.nl/?url=github.com/QingJ01.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@QingJ01" /></a>
<a href="https://github.com/3215"><img src="https://images.weserv.nl/?url=github.com/3215.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@3215" /></a>
<a href="https://github.com/saifulaiub123"><img src="https://images.weserv.nl/?url=github.com/saifulaiub123.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@saifulaiub123" /></a>
<a href="https://github.com/PietFourie"><img src="https://images.weserv.nl/?url=github.com/PietFourie.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@PietFourie" /></a>
<a href="https://github.com/mhmdkrmabd"><img src="https://images.weserv.nl/?url=github.com/mhmdkrmabd.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@mhmdkrmabd" /></a>
<a href="https://github.com/DemeulemeesterxMaxime"><img src="https://images.weserv.nl/?url=github.com/DemeulemeesterxMaxime.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@DemeulemeesterxMaxime" /></a>
<a href="https://github.com/HoodBlah"><img src="https://images.weserv.nl/?url=github.com/HoodBlah.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@HoodBlah" /></a>
<a href="https://github.com/SeanPedersen"><img src="https://images.weserv.nl/?url=github.com/SeanPedersen.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@SeanPedersen" /></a>
<a href="https://github.com/andersmmg"><img src="https://images.weserv.nl/?url=github.com/andersmmg.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@andersmmg" /></a>
<a href="https://github.com/chirag127"><img src="https://images.weserv.nl/?url=github.com/chirag127.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@chirag127" /></a>
<a href="https://github.com/jasnoorgill"><img src="https://images.weserv.nl/?url=github.com/jasnoorgill.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@jasnoorgill" /></a>
<a href="https://github.com/allababbot"><img src="https://images.weserv.nl/?url=github.com/allababbot.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@allababbot" /></a>
<a href="https://github.com/johan-droid"><img src="https://images.weserv.nl/?url=github.com/johan-droid.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@johan-droid" /></a>
<a href="https://github.com/redenfire"><img src="https://images.weserv.nl/?url=github.com/redenfire.png&w=60&h=60&fit=cover&mask=circle" width="60" alt="@redenfire" /></a>

## 服务条款审查

针对每个提供商的 ToS 重新审查了自托管、单用户、个人用途的设置（2026 年 5 月）。摘要：

| 提供商 | 结论 | 备注 |
|---|---|---|
| Google Gemini | ⚠️ 需注意 | 2026 年 3 月 ToS 将范围缩小到*"专业或商业用途，非消费用途"* — 自托管开发者代理仍然可以辩护，但该条款是新增的。 |
| Groq | ✅ 大概率可以 | GroqCloud 服务协议允许客户应用程序集成。 |
| Cerebras | ✅ 大概率可以 | 允许；明确禁止出售/转让 API 密钥。 |
| Mistral | ✅ 大概率可以 | API 允许用于个人/内部商业用途。 |
| OpenRouter | ✅ 大概率可以 | 2026 年 4 月 ToS 加强了禁止转售/禁止竞争服务的条款；私有单用户代理仍然没问题。 |
| Cloudflare Workers AI | ⚠️ 含糊不清 | 没有反代理条款；受一般自助订阅协议涵盖。 |
| NVIDIA NIM | ⚠️ 需注意 | 试用 ToS §1.2 / §1.4：*"仅供评估，非生产用途。"* 免费访问是一个持续的 40 RPM 速率限制（2025 年的积分系统已停止使用），但仅限评估的范围仍然有效。 |
| GitHub Models | ⚠️ 需注意 | 免费额度明确限定为*"实验"*和*"原型设计"。* |
| Cohere | ❌ 避免 | 条款 §14 仍然禁止*"个人、家庭或家庭用途。"* |
| Zhipu (open.bigmodel.cn) | ✅ 大概率可以 | 个人/非商业研究豁免条款仍在平台文档中。 |
| Z.ai (api.z.ai) | ⚠️ 需注意 | 新条目 — 新加坡实体（与 Zhipu CN 不同）。§III.3(l) 反流量重定向条款可能被解读为针对代理；没有明确的个人用途豁免。 |
| Ollama Cloud | ✅ 大概率可以 | 新条目 — 免费计划允许访问云模型（1 个并发、5 小时会话上限）。未发现反代理/反转售条款。*（集成追踪在 #14。）* |
| OVH AI Endpoints | ✅ 大概率可以 | 新条目（2026 年 6 月）— 匿名访问已有官方文档（每个 IP 每个模型 2 次/分钟请求）。OVH 保留引入 Token/消耗量上限的权利。 |

让大多数提供商满意的经验法则：**每个提供商一个账户**，**不转售**，**不与他人共享你的端点**，**不要把免费额度当作付费生产后端来滥用**。这仅供参考，不是法律建议——请阅读每个提供商的 ToS 并自行判断。

自 2026 年 4 月审查以来移除的内容：Hugging Face、Moonshot 和 MiniMax 的直接集成已从目录中移除（HF — 工具调用格式问题；Moonshot — 转为仅付费；MiniMax — 被 OpenRouter 的 `minimax/minimax-m2.5:free` 路由取代）。

## 免责声明

**本项目仅供个人实验和学习使用，不适合生产环境。** 免费额度的存在是为了让开发者可以在其上进行原型开发；它们不是稳定的、受支持的推理基础设施，不应被当作这样的东西对待。如果你在 FreeLLMAPI 上构建了真正的东西，请在发布前切换到付费 API。你与每个上游提供商的关系受你创建账户时接受的条款约束——当流量通过本项目代理时这些条款仍然适用，你有责任遵守它们。

## Star History

[![Star History Chart](https://api.star-history.com/chart?repos=tashfeenahmed/freellmapi&type=date&legend=top-left)](https://www.star-history.com/?repos=tashfeenahmed%2Ffreellmapi&type=date&legend=top-left)

## License

[MIT](./LICENSE)
