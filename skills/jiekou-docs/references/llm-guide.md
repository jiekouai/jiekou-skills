# LLM API 指南

接口AI 提供兼容 OpenAI 的 API，持续更新模型目录。

Verified on: 2026-03-12

## 目录

- [快速配置](#快速配置)
- [基础聊天补全](#基础聊天补全)
- [关键参数](#关键参数)
- [函数调用（工具使用）](#函数调用工具使用)
- [视觉（图像输入）](#视觉图像输入)
- [结构化输出（JSON 模式）](#结构化输出json-模式)
- [高级功能](#高级功能)
- [API 参考](#api-参考)

---

## 快速配置

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://api.jiekou.ai/openai",
    api_key="<YOUR_API_KEY>",
)
```

---

## 基础聊天补全

```python
response = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ],
    max_tokens=512,
    stream=True,  # 长响应建议开启流式
)

for chunk in response:
    print(chunk.choices[0].delta.content or "", end="")
```

---

## 关键参数

### 模型选择

- 浏览完整模型列表：https://jiekou.ai
- 通过 API 查询：`GET https://api.jiekou.ai/openai/v1/models`
- 模型名称格式：`provider/model-name`（例如 `deepseek/deepseek-r1`）

```bash
curl https://api.jiekou.ai/openai/v1/models \
  -H "Authorization: Bearer $JIEKOU_API_KEY"
```

### 输出控制

| 参数 | 说明 | 典型值 |
|---|---|---|
| `max_tokens` | 最大响应长度 | 512–4096 |
| `temperature` | 随机性（0 = 确定性，2 = 高创造性） | 0.7 |
| `top_p` | 核采样概率阈值 | 0.9 |
| `stream` | 流式返回响应块 | true |

### 频率控制

| 参数 | 说明 |
|---|---|
| `presence_penalty` | 对已出现 token 施加惩罚，鼓励引入新话题 |
| `frequency_penalty` | 按出现频率施加惩罚，减少重复内容 |
| `stop` | 遇到指定序列时停止生成 |

---

## 函数调用（工具使用）

允许 LLM 调用外部函数或 API。

### 支持的模型

- `minimax/minimax-m2.5`
- `qwen/qwen3-coder-next`
- `zai-org/glm-4.7-flash`
- [查看全部支持模型](https://jiekou.ai)

### 示例

定义工具列表，通过 `tools` 参数传入模型，然后解析返回的工具调用信息。

```python
import json

tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "City and state, e.g. San Francisco, CA"
                    }
                },
                "required": ["location"]
            }
        }
    }
]

response = client.chat.completions.create(
    model="deepseek/deepseek-v3.2",
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
    tools=tools,
)

tool_call = response.choices[0].message.tool_calls[0]
print(tool_call.function.name)       # "get_weather"
print(tool_call.function.arguments)  # '{"location": "Tokyo, Japan"}'
```

---

## 视觉（图像输入）

使用视觉语言模型理解和分析图像内容。

### 支持的模型

- `qwen/qwen2-vl-72b-instruct`
- `meta-llama/llama-4-maverick`
- [查看全部视觉模型](https://jiekou.ai)

### 通过 URL 传入图像

```python
response = client.chat.completions.create(
    model="qwen/qwen2-vl-72b-instruct",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://example.com/image.jpg",
                        "detail": "high"  # 可选：high、low、auto
                    }
                },
                {"type": "text", "text": "Describe this image."}
            ]
        }
    ],
)
```

### 通过 Base64 传入图像

```python
import base64

with open("image.jpg", "rb") as f:
    base64_image = base64.b64encode(f.read()).decode("utf-8")

response = client.chat.completions.create(
    model="qwen/qwen2-vl-72b-instruct",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image}",
                        "detail": "high"
                    }
                },
                {"type": "text", "text": "What text is in this image?"}
            ]
        }
    ],
)
```

---

## 结构化输出（JSON 模式）

强制 LLM 输出符合指定 Schema 的合法 JSON。

### 示例

```python
response = client.chat.completions.create(
    model="deepseek/deepseek-v3.2",
    messages=[
        {"role": "system", "content": "Extract expense info as JSON."},
        {"role": "user", "content": "I spent $50 on lunch and $30 on coffee today."}
    ],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "expenses",
            "schema": {
                "type": "object",
                "properties": {
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "description": {"type": "string"},
                                "amount": {"type": "number"},
                                "category": {"type": "string"}
                            },
                            "required": ["description", "amount"]
                        }
                    }
                },
                "required": ["items"]
            }
        }
    }
)
```

---

## 高级功能

### 提示缓存

自动缓存重复的提示前缀，降低延迟并减少费用。

### 推理模型

`deepseek/deepseek-r1` 等推理模型会在响应中附带推理过程：

```python
print(response.choices[0].message.reasoning_content)
```

### 流式输出

输出内容较长时，建议始终开启流式模式以避免请求超时：

```python
stream = client.chat.completions.create(
    model="deepseek/deepseek-r1",
    messages=[...],
    stream=True,
)
for chunk in stream:
    print(chunk.choices[0].delta.content or "", end="")
```

---

## API 参考

- **接口地址**：`POST https://api.jiekou.ai/openai/v1/chat/completions`
- **完整文档**：https://jiekou.ai/docs/models/reference-llm-create-chat-completion
- **限流规则**：依账户等级与所用模型而定，请在文档或控制台中查看当前限制
