# 接口AI 快速入门

5 分钟上手接口AI LLM(大语言)、图像、视频、音频 API。

Verified on: 2026-03-12

## 1. 获取 API Key

1. 登录 [jiekou.ai](https://jiekou.ai)（支持 Google / GitHub / 邮箱注册）
2. 进入 [Key 管理](https://jiekou.ai/settings/key-management)
3. 点击"创建 API Key"并妥善保存

## 2. 发起第一个 LLM(大语言) 请求

接口AI **兼容 OpenAI SDK**，只需替换 Base URL，无需修改其他代码。

### Python（OpenAI SDK）

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.jiekou.ai/openai",
    api_key="<YOUR_API_KEY>",
)

response = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ],
    max_tokens=512,
)
print(response.choices[0].message.content)
```

### cURL

```bash
curl https://api.jiekou.ai/openai/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $接口AI_API_KEY" \
  -d '{
    "model": "deepseek/deepseek-r1",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Hello!"}
    ],
    "max_tokens": 512
  }'
```

其他语言或 SDK 同理：使用 OpenAI 兼容的客户端，将 Base URL 指向 `https://api.jiekou.ai/openai` 即可。

### 选择模型

- **浏览全部模型**：[jiekou.ai](https://jiekou.ai)
- **通过 API 列出**：`GET https://api.jiekou.ai/openai/v1/models`

### 推荐模型

> 模型上线与下线较频繁，以 [jiekou.ai](https://jiekou.ai) 页面为准。

| 模型 | 适用场景 |
|---|---|
| `deepseek/deepseek-r1` | 推理、代码 |
| `deepseek/deepseek-v3` | 通用对话 |
| `qwen/qwen3-235b-a22b` | 长上下文 |
| `meta-llama/llama-4-maverick` | 高速推理 |

## 3. 图像、视频、音频 API

接口AI 聚合了业内主流的多模态 API，覆盖图像生成、视频生成和语音合成三大方向，例如 lux、Seedream、Qwen-Image、GPT、Gemini、Hunyuan、Midjourney、Z Image、Grok Imagine、Wan、Kling、Minimax、Vidu、PixVerse、Seedance、OpenAI Sora、Veo、Heygen、ElevenLabs voice、GLM voice、Fish audio 等热门系列 API。

视频生成还提供了[统一接口](https://jiekou.ai/docs/models/reference-unified-video-generation)，抹平各模型的差异，一套代码切换多个模型。

完整的参数说明和调用示例，可通过 [MCP 工具](https://jiekou.mintlify.app/mcp)直接查询。


## 4. 充值

新用户注册后可获代金券，可直接体验。额度用尽后，前往[充值页面](https://jiekou.ai/billing)按需充值。

## 下一步

- [LLM API 指南](llm-guide.md) — 流式输出、函数调用、多模态等高级功能
- [集成指南](integrations.md) — 接入 LangChain、Cursor 等主流工具
