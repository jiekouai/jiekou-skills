# 常见问题与 FAQ

Verified on: 2026-03-12

## 目录

- [充值与计费](#充值与计费)
- [API 配置与技术接入](#api-配置与技术接入)
- [图像与视频生成](#图像与视频生成)
- [速率限制与性能](#速率限制与性能)
- [账户与代金券](#账户与代金券)
- [发票与合规](#发票与合规)
- [其他常见问题](#其他常见问题)

---

## 充值与计费

### 1. 充值后余额未实时到账怎么办？

正常情况下充值是随充随到账。如遇到账延迟，请提供以下信息联系客服：

- 账号 ID (UUID)
- 充值订单号 (product_xxx)
- 支付截图

注：系统偶尔会出现延迟，一般会在排查后手动到账。如确认是系统问题，平台通常会发放代金券作为补偿。

### 2. 充值比例是多少？人民币如何充值？

充值按照美元计算，您可以通过支付宝直接充值，点击充值页面即可看到实时汇率。

### 3. 如何避免充值失败？

充值失败通常由两个主要原因引起：

- **发卡行拒绝**。可能由于以下原因导致，请检查或联系您的发卡行了解详情：
  - 相应的支付通道未激活
  - 信用卡已过期或被冻结
  - 信用卡余额不足
  - 卡号不正确
  - 安全码不正确
- **支付通道的风控措施**。请检查并进行必要的调整：
  - 设备 ID 关联的卡片数量过多
  - 使用此电子邮件地址拒绝的卡片数量非常高
  - 此卡在 Stripe 网络中与此设备 ID 首次出现的时间非常短
  - 与此电子邮件地址关联的授权率非常低
  - 电子邮件地址上的姓名与卡上的姓名不匹配

### 4. 价格是如何计算的？

不同模型/能力按不同计费方式收取：

- **大语言模型 / Embedding / Reranker**：按 token 计费，包含 input / output / cache write / cache read 等维度
- **图片 / 视频 / 音频**：按调用次数收费

### 5. 如何查看详细的账单和用量？

- 账单总览：https://jiekou.ai/billing/transactions
- 详细用量：https://jiekou.ai/billing/details （可查看各模型的 Token 用量明细）
- 价格表：https://jiekou.ai/pricing

### 6. 为什么调用量很少却被扣了大量费用？

请检查以下情况：

- **模型选择**：Claude Opus 4.5 等高端模型消耗较快（输入 11W token 可能就需 0.45 美金以上）
- **Token 计算**：Gemini 等模型的图片输入按分辨率计算 Token，高分辨率图片可能产生大量 Token
- **缓存计费**：部分模型（如 Claude）的 Prompt Caching 如未命中，会以"缓存输入"价格计费，可能比普通输入更贵

### 7. 为什么代金券/余额还有，却提示余额不足(403)？

可能原因包括：

- 账户确实费用不足（现金 + 代金券总和不足）
- 系统计费延迟显示（实际已扣完）
- 超过单分钟 RPM/TPM 限制被限流（错误提示可能显示为余额不足）

### 8. 免费模型为什么也在扣费？

请确认是否在使用过程中切换过模型。如果在付费模型会话中切换至免费模型，可能因上下文 tokens 计算产生费用。建议检查账单详情中的模型调用记录。

### 9. 支持退款吗？如何申请？

一般情况下，已使用的余额不支持退款。如确需退款（如平台无法满足业务需求），请提供：

- 账号 ID (UUID)
- 注册邮箱
- 退款原因

注：通过第三方工具（如 Cursor）使用遇到兼容性问题通常不支持退款，建议先使用小额充值测试。

### 10. 退款是原路返回吗？

是的，退款会原路退回至原支付账户。如为公司充值，需与财务确认原支付渠道。

---

## API 配置与技术接入

### 1. API 基础 URL 应该填什么？

根据协议不同，主要有以下几种：

- **OpenAI 兼容格式**：`https://api.jiekou.ai/openai` 或 `https://api.jiekou.ai/openai/v1/chat/completions`
- **Anthropic 原生协议**：`https://api.jiekou.ai/anthropic`（用于 Claude Code 等工具）
- **生图/生视频专用**：`https://api.jiekou.ai/v3/`（如 Gemini、Nano Banana 等）

注意：如遇到 404 错误，请检查 URL 末尾是否多写了 `/v1`，不同工具对路径拼接逻辑不同。

### 2. 调用时返回 401 "无效令牌" 怎么办？

1. 确认 API Key 已正确创建：https://jiekou.ai/settings/key-management
2. 确认请求头中 Authorization 格式为：`Bearer sk_xxxxxx`
3. 如使用 Claude Code，环境变量应设置为 `ANTHROPIC_AUTH_TOKEN=sk_xxxxx`（不需要加 Bearer 前缀，工具会自动添加）

### 3. 调用返回 404 "page not found" 怎么排查？

常见原因：

- URL 错误：如使用了 `/v3/glm-asr` 却拼写错误
- 模型路由错误：如 Codex 模型需要使用 `/v1/responses` 而非 `/v1/chat/completions`
- 工具自动拼接问题：部分工具（如 cc-switch）会自动添加 `/chat/completions`，此时 Base URL 不应包含该路径

### 4. 如何在 Claude Code 中配置 Jiekou.AI？

环境变量配置如下：

**Windows cmd：**

```
set ANTHROPIC_BASE_URL=https://api.jiekou.ai/anthropic
set ANTHROPIC_AUTH_TOKEN=sk_您的API密钥
set ANTHROPIC_MODEL=claude-opus-4-1-20250805
set ANTHROPIC_SMALL_FAST_MODEL=claude-sonnet-4-20250514
```

**Mac/Linux bash：**

```
export ANTHROPIC_BASE_URL=https://api.jiekou.ai/anthropic
export ANTHROPIC_AUTH_TOKEN=sk_您的API密钥
export ANTHROPIC_MODEL=claude-opus-4-1-20250805
export ANTHROPIC_SMALL_FAST_MODEL=claude-sonnet-4-20250514
```

参考文档：https://docs.jiekou.ai/docs/integration/claudecode

### 5. Claude Code 提示强制登录/需要验证怎么解决？

最新版 Claude Code 可能强制要求登录。解决方案：

- 使用 Cline 插件替代（VSCode 插件市场搜索）
- 或配合 cc-switch 等转发工具使用

### 6. GPT-5.1 / Codex 模型如何调用？为什么返回 400 错误？

GPT-5.1 系列（包括 Codex）需要使用 OpenAI 的 Responses API，而非 Chat Completions：

```bash
curl "https://api.jiekou.ai/openai/v1/responses" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <API Key>" \
  -d '{
    "model": "gpt-5.1-codex",
    "input": [...],
    "max_output_tokens": 64000
  }'
```

### 7. Claude 4.5 返回 "thinking block" 相关错误怎么办？

错误通常提示：`Expected 'thinking' or 'redacted_thinking', but found 'text'`。这是因为 Claude 4.5 启用 Thinking 功能后，要求上下文必须包含特定的思考块格式。建议：

- 清除对话历史重新开始
- 或禁用 Thinking 功能（如不需要）
- 使用原生 Anthropic 协议而非 OpenAI 兼容协议（后者对 Thinking 支持可能不完整）

### 8. 调用 Claude API 返回 "输入过长" 错误？

Claude 模型对输入长度有限制（通常 max_tokens 最大支持 64000）。请检查：

- 输入文本长度
- Base64 图片的大小（过大图片建议先压缩）
- 历史对话上下文累积长度

---

## 图像与视频生成

### 1. Nano Banana Pro 和 Light 版本有什么区别？

- **Nano Banana Pro**：直连官方正式版，稳定性好，支持完整功能（如参考图）
- **Nano Banana Pro Light**：逆向/破解版本，价格便宜，但稳定性较差，某些特性不支持（如参考图功能可能失效），且可能触发内容审核（PROHIBITED_CONTENT）

建议：生产环境建议使用非 Light 版本，测试环境可使用 Light 版本节约成本。

### 2. 图像高清化(Upscale)接口只是放大了图片，没有变清晰？

目前 Upscale 接口主要是通过提高分辨率实现"高清化"，并不具备 AI 重绘细节的能力。如需真正的细节增强，建议使用：

- Midjourney 的 Upscale 功能（需配合 MJ 生成的图片使用）
- 或其他专门的图像增强模型

### 3. 生图接口返回 500 错误或排队时间过长？

可能原因：

- **资源紧张**：高峰期（如下午、晚上）生视频/生图任务可能排队（TASK_STATUS_QUEUED），建议避开高峰期
- **Light 版不稳定**：如使用 Light 版本，可能出现间歇性失败，建议重试或改用正式版
- **内容审核**：提示词触发安全审核（如 Gemini 返回 PROHIBITED_CONTENT），请修改提示词

### 4. 生成的图片 URL 无法访问？

平台返回的 COS URL 是预签名链接（Presigned URL），通常可以公开访问。如无法访问请检查：

- 是否在复制 URL 时截断或添加了多余字符
- 如用于微信小程序等场景，需配置 CORS（跨域）支持，平台已默认配置，如仍报错请联系客服

### 5. Sora 2 / Veo 3.1 生成慢或失败？

生视频属于重资源任务：

- **排队正常**：高峰期可能需要等待 2-30 分钟
- **任务丢失**：如超过 30 分钟仍无结果且查询返回 "task not found"，可能是任务失败，建议重试
- **横竖屏问题**：部分版本（如 Sora2）的横竖屏参数可能失效，建议先使用默认横屏

---

## 速率限制与性能

### 1. 生图/生视频模型有 RPM 限制吗？

是的，各模型 RPM（每分钟请求数）限制不同：

- Veo 3.1：约 50 RPM
- Seedance：约 200 RPM
- Kling：视具体模型而定

### 2. 可以申请增加 RPM 限制吗？

可以。允许根据使用需求灵活升级 RPM，请联系我们并告知您的需求。

### 3. 如果实际使用未达到承诺的 RPM 会怎样？

如果用户的实际 RPM 连续一周低于承诺水平，平台将调整速率限制，政策如下（以较低者为准）：

- 将限制减少到过去一周的峰值 RPM
- 恢复到模型的默认速率限制

### 4. 返回 429 "server overload" 或 "RPM limit exceeded" 怎么办？

这是触发了速率限制。各模型限制请参考：https://docs.jiekou.ai/docs/model/llm-rate-limits

临时解决方案：

- 降低请求频率
- 在代码中加入指数退避重试（exponential backoff）
- 联系客服根据充值金额提升 RPM 配额

### 5. 模型响应经常出现超时（Timeout）？

特别是 Gemini 等图像编辑模型，处理时间较长（可能 1-10 分钟）。建议：

- 将超时时间设置为 10 分钟以上
- 使用异步任务模式（如适用）
- 避免在高峰期调用重型任务

---

## 账户与代金券

### 1. 邀请 / 注册代金券如何获得？

- **新用户**：
  - 通过邀请码完成注册并绑定 Github 后，获得 $2 代金券
  - 填写问卷后，再获得 $1 代金券
- **老用户**：
  - 新用户通过邀请链接或填写邀请码注册，并绑定 Github 后，老用户获得 $1 代金券
  - 受邀用户的前 5 次充值也会按一定比例给老用户发放代金券（以活动规则为准）

详细请参考活动页：https://jiekou.ai/referral

### 2. 绑定 Github 后的 $2 代金券没有到账？

请确认：

1. 完成 Github 绑定
2. 注册时填写了邀请码（如注册时未填，可能无法获得，可联系客服补发）
3. 刷新页面或重新登录查看

### 3. 代金券怎么使用？

代金券用于抵扣账户在按量计费调用模型时产生的账单费用，在有效期内会自动触发抵扣。

### 4. 为什么代金券显示为 0，但我明明没用完？

可能存在系统延迟或显示 Bug。请提供 UUID 联系客服核查。历史上有过因 Bug 导致显示异常的情况，实际余额可能仍在。

### 5. 个人开发者有优惠政策吗？

春节前后会推出订阅包活动，届时请关注订阅包优惠政策。

### 6. 有包月或者包年套餐吗？

春节前后会推出订阅包活动，届时请关注订阅包优惠政策。

---

## 发票与合规

### 1. 支持开具国内发票（增值税专票/普票）吗？

目前平台主要提供海外服务，只能提供 Invoice（形式发票），无法开具中国大陆的增值税发票。如需报销，请提供公司抬头和税号申请 Invoice。

- 进入【充值记录】，点击对应记录的"发票-下载"
- 充值后 Stripe 也会自动向您的邮箱发送 invoice 和 receipt

### 2. 微信小程序审核需要"第三方技术在用证明"怎么获取？

可提供平台的备案信息截图，或访问 https://beian.cac.gov.cn 查询平台备案情况作为辅助证明。

---

## 其他常见问题

### 1. 对话历史记录在哪里查看？

平台不保存用户对话数据（隐私保护原则）。如需历史记录功能，建议：

- 使用第三方客户端如 ChatBox、Cherry Studio 等
- 在本地保存日志

### 2. Prompt Caching（提示词缓存）为什么显示命中率为 0？

请确认：

1. 使用的模型支持缓存（如 Claude 3.5 Sonnet、GPT-4o 等，GPT-5-mini 目前不支持）
2. 缓存需要预设的提示词前缀完全一致（逐字符匹配）
3. 首次调用会写入缓存（产生写入费用），后续调用才能命中

### 3. 平台模型是代理还是自部署？

闭源模型（如 OpenAI、Claude、Gemini）为官方 API 代理；开源模型部分为平台自行部署。所有模型均通过统一网关提供稳定接入。

---

## 联系支持

如以上 FAQ 无法解决您的问题，请通过以下方式联系技术支持：

- 官网右下角 - 企业微信/微信技术支持群（推荐，响应最快）
- 提供信息格式：
  - 问题描述 + 截图
  - 账号 ID (UUID)
  - Trace ID（如有，通常在错误信息中）
  - 请求参数（脱敏后）
