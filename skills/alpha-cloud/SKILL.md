---
name: alpha-cloud
description: Code Puppy 云平台用法:云任务派发(cloud_dispatch/status/await/artifacts 四个 MCP 工具)的信封格式、预算护栏、计费语义与错误处理。当用户要求"云端执行/云任务/派发任务到云上",或 cloud_* 工具调用报错需要解释时使用。
---

# Code Puppy 云平台用法

## 任务信封(cloud_dispatch)

- `autonomy: "pipeline"` —— 步骤可预先写死的任务。必填 `kind` + `input`。kind 目录:`research`(联网调研)、`code-review`、`docs`、`office-report`(docx/pptx/xlsx)、`data-analysis`(pandas/图表)、`bugfix`(patch+测试)、`migration`(多步重构)。
- `autonomy: "bounded-agent"` —— 开放式目标。必填 `objective` + `capabilities[]`(如 `["web_search"]`、`["code_exec"]`;声明能力而非 tier,平台自动路由运行时)。
- `budget`(可选,从紧):`{max_iter ≤50, max_tokens ≤500000, max_wall_clock_sec ≤1800}`。超预算任务会被平台硬熔断(402)。
- `output_schema`(可选):要结构化结果时给 JSON Schema。

## 计费语义(务必告知用户的部分)

- 云任务**恒计费**(平台代付模型,按账户会员窗口→¥钱包瀑布扣)。
- 每次模型调用前平台预授权:额度尽 → 402 且**不调模型**(不会产生费用)。
- 账单按 API key 归因,用户可在 https://codepuppy.cn/billing 查每笔用量。

## 错误处理

- 401:ALPHA_API_KEY 无效/已撤销 → 让用户去 https://codepuppy.cn/keys 重新生成并更新环境变量。
- 402:额度不足 → 充值或等会员窗口重置;job 内 402 = 预算熔断(job_budget)。
- 403 lacks scope 'cloud':key 缺 cloud scope → 重新生成时勾选「云任务」。
- 503:计费服务暂不可达(fail-closed 保护钱包)→ 稍后重试。

## 进度与产物

- `cloud_await`:有界等待(≤25s/次),长任务循环调用或改告知用户稍后查。
- `cloud_artifacts`:小结果内联返回;大 artifact 给认证下载 URL。
- 状态 phase 是中性的(routing / agent.running 等),没有更细的内部信息可查。
