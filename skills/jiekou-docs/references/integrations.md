# 第三方工具接入

**API Base URL**: `https://api.jiekou.ai/openai/v1`  
**获取 API Key**: https://jiekou.ai/settings/key-management

Verified on: 2026-03-12

---

## Chatbox

> 开源对话应用框架，支持客户服务、知识问答、内容创作等场景。

**官方文档**: https://jiekou.ai/docs/integration/chatbox.md

**配置步骤**：
1. 获取 API Key（见上方链接）
2. 在 Chatbox 设置中：
   - **API Base URL**: `https://api.jiekou.ai/openai/v1`
   - **API Key**: 填入你的 JieKou API Key
   - **模型**: 从模型广场选择所需模型 ID

---

## Claude Code

> Anthropic 推出的终端智能体编程工具，理解代码库并执行编程任务。

**官方文档**: https://jiekou.ai/docs/integration/claudecode.md

**安装**：
```bash
npm install -g @anthropic-ai/claude-code
```

**配置环境变量并启动**：
```bash
{
  export ANTHROPIC_BASE_URL="https://api.jiekou.ai/anthropic"
  export ANTHROPIC_AUTH_TOKEN="<API Key>"
  export ANTHROPIC_MODEL="claude-opus-4-1-20250805"
  export ANTHROPIC_SMALL_FAST_MODEL="claude-sonnet-4-20250514"
}
cd <your-project-directory>
claude .
```

---

## OpenAI Codex CLI

> OpenAI 推出的本地运行编程终端智能体。

**官方文档**: https://jiekou.ai/docs/integration/codex.md

**安装**：
```bash
npm install -g @openai/codex
# macOS 可用 Homebrew
brew install codex
```

**配置** `~/.codex/config.toml`：
```toml
model = "gpt-5"
model_provider = "jiekou"

[model_providers.jiekou]
name = "JIEKOU using Chat Completions"
base_url = "https://api.jiekou.ai/openai/v1"
env_key = "OPENAI_API_KEY"
wire_api = "chat"
query_params = {}
```

**运行**：
```bash
OPENAI_API_KEY=<API_KEY> codex
```

**VSCode 扩展配置**：
```bash
{
cat << 'EOF' | tee $HOME/.codex/pcodex.sh
#!/bin/bash
OPENAI_API_KEY=sk_xxx /opt/homebrew/bin/codex "$@"
EOF
chmod +x $HOME/.codex/pcodex.sh
}
```

在 VSCode `settings.json` 中添加：
```json
{
  "chatgpt.cliExecutable": "/home/path/.codex/pcodex.sh"
}
```

---

## DeepChat

> 企业级智能对话平台，适用于个人知识管理、企业知识库、自定义对话场景。

**官方文档**: https://jiekou.ai/docs/integration/deepchat.md

**配置步骤**：
1. 获取 API Key 和所需模型 ID（推荐：`claude-sonnet-4-5`、`gpt-5`、`gpt-4o`、`gemini-2.5-pro`）
2. 打开 DeepChat，在【选择服务商】中开启【JieKou.AI】
3. 填入 API Key
4. 在【服务商设置】→【JieKou.AI】中添加模型（填入模型 ID）

---

## LangBot

> 开源 LLM 原生 IM 机器人平台，支持飞书、钉钉、QQ、企业微信、Discord、Slack 等平台。具备 Agent、RAG、MCP 功能。

**官方文档**: https://jiekou.ai/docs/integration/langbot.md  
**项目地址**: https://github.com/RockChinQ/LangBot

**Docker 部署**：
```bash
git clone https://github.com/langbot-app/LangBot
cd LangBot/docker
docker compose up
```

WebUI 地址：http://127.0.0.1:5300

**模型配置参数**：
| 字段 | 值 |
|------|-----|
| 模型提供商 | 接口AI |
| 请求 URL | `https://api.jiekou.ai/openai` |
| API Key | 你的 JieKou API Key |
| 模型名称 | 从模型广场复制（如 `gpt-5`、`claude-sonnet-4-5`） |

---

## RAGFlow

> 基于深度文档理解的开源 RAG 引擎，支持企业级知识管理与 AI 问答。

**官方文档**: https://jiekou.ai/docs/integration/ragflow.md  
**RAGFlow 文档**: https://ragflow.io/docs/dev/

**配置步骤**：
1. 获取 API Key 和模型 ID
2. 在 RAGFlow 控制台 → 【模型供应商】→ 找到 **OpenAI-API-Compatible** → 点击【添加模型】
3. 填写配置：
   - **Base URL**: `https://api.jiekou.ai/openai/v1`
   - **API Key**: 你的 JieKou API Key
   - **模型 ID**: 如 `gpt-5`、`claude-sonnet-4-5`、`gemini-2.5-pro`

---

## OpenManus

> Manus 的开源复刻版，基于 RL 调优的 LLM 智能体，适用于 GAIA、AgentBench 等基准测试场景。

**官方文档**: https://jiekou.ai/docs/integration/openmanus.md  
**项目地址**: https://github.com/FoundationAgents/OpenManus

**安装**：
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
git clone https://github.com/FoundationAgents/OpenManus.git
cd OpenManus
uv venv --python 3.12
source .venv/bin/activate  # Windows: .venv\Scripts\activate
uv pip install -r requirements.txt
```

**配置** `config/config.toml`：
```bash
cp config/config.example.toml config/config.toml
```

```toml
[llm]
model = "gpt-4o"
base_url = "https://api.jiekou.ai/openai"
api_key = "<你的 JieKou API Key>"
max_tokens = 4096
temperature = 0.0

[llm.vision]
model = "gpt-4o"
base_url = "https://api.jiekou.ai/openai"
api_key = "<你的 JieKou API Key>"
```

**启动**：
```bash
python main.py
```

---

## DeepSearcher

> 结合 LLM 与向量数据库（Milvus 等），基于私有数据执行搜索、评估和推理，适用于企业知识管理、智能问答。

**官方文档**: https://jiekou.ai/docs/integration/deepsearcher.md  
**项目地址**: https://github.com/zilliztech/deep-searcher

**安装**：
```bash
git clone https://github.com/zilliztech/deep-searcher.git
cd deep-searcher
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

**配置接入 JieKou.AI**（在 `examples/basic_example.py` 的 `config = Configuration()` 后添加）：
```bash
export JIEKOU_API_KEY="你的 JieKou API Key"
```

```python
config.set_provider_config("llm", "JiekouAI", {"model": "claude-sonnet-4-5-20250929"})
config.set_provider_config("embedding", "JiekouAIEmbedding", {"model": "qwen/qwen3-embedding-8b"})
```

**运行**：
```bash
python examples/basic_example.py
```

---

## OpenCode

> 面向开发者的开源 AI 编程智能体，终端原生 TUI/CLI 体验，支持需求讨论、代码生成、重构、调试等工作流。

**官方文档**: https://jiekou.ai/docs/integration/opencode.md  
**OpenCode 官网**: https://opencode.ai

**安装**：
```bash
curl -fsSL https://opencode.ai/install | bash
```

**启动**：
```bash
cd /path/to/project
opencode
```

**配置接入 JieKou API**（`~/.config/opencode/opencode.json` 或项目根目录 `opencode.json`）：
```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "JieKou",
      "options": {
        "baseURL": "https://api.jiekou.ai/openai/v1"
      },
      "models": {
        "claude-sonnet-4-5-20250929": {
          "name": "claude-sonnet-4-5-20250929"
        },
        "gpt-5.2": {
          "name": "gpt-5.2"
        }
      }
    }
  }
}
```

**npm 包选择**：
| npm 包 | API 类型 | 适用场景 |
|--------|---------|---------|
| `@ai-sdk/openai-compatible` | Chat Completion API | GPT、Claude 等大部分模型 |
| `@ai-sdk/openai` | Response API | Codex 系列模型（如 `gpt-5.1-codex`） |

配置 Key：在 opencode 内使用 `/connect` 选择供应商并填写 API Key；使用 `/models` 命令切换模型。

---

## OpenClaw

> 开源个人 AI 助手平台，可接入 WhatsApp、Telegram、Slack、飞书等，支持邮件、日历、浏览器、文件系统和脚本执行，具备持久记忆与多模型接入能力。

**官方文档**: https://jiekou.ai/docs/integration/openclaw.md  
**OpenClaw 文档**: https://docs.openclaw.ai/

**系统要求**: Node >=22，macOS / Linux / Windows(WSL2)

**安装**：
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**配置接入 JieKou API**（修改 `~/.openclaw/openclaw.json`）：
```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "Jiekou": {
        "baseUrl": "https://api.jiekou.ai/openai/v1",
        "apiKey": "sk_xxxxxxxxxxxxxxxxxx",
        "api": "openai-completions",
        "models": [
          {
            "id": "gpt-5.4",
            "name": "gpt-5.4 (Custom Provider)",
            "reasoning": false,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 16000,
            "maxTokens": 4096
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "jiekou/gpt-5.4"
      },
      "models": {
        "Jiekou/gpt-5.4": {
          "alias": "gpt-5.4"
        }
      },
      "workspace": "~/.openclaw/workspace",
      "compaction": { "mode": "safeguard" },
      "maxConcurrent": 4,
      "subagents": { "maxConcurrent": 8 }
    }
  }
}
```
