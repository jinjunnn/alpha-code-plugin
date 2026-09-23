---
name: alpha-cloud
description: Code Puppy 云平台用法:云任务派发(cloud_dispatch/status/await/artifacts/cancel/web_search 六个 MCP 工具)的信封格式、预算护栏、计费语义与错误处理。当用户要求"云端执行/云任务/派发任务到云上",或 cloud_* 工具调用报错需要解释时使用。
---

# Code Puppy 云平台用法

## 六个 MCP 工具

| 工具 | 干什么 |
| --- | --- |
| `cloud_dispatch` | 派一个云任务,返回 `job_id` |
| `cloud_status` | 查一次任务状态 |
| `cloud_await` | 有界等待到终态(≤25s/次) |
| `cloud_artifacts` | 取任务产物的**描述符**与结果 |
| `cloud_cancel` | 取消一个在跑的任务(止损;已终态的任务会如实回它的真实终态,结果与产物保留) |
| `cloud_web_search` | 让平台代为联网搜索(key 在平台侧;**这一个按次计费**,见「计费语义」) |

`cloud_dispatch` 的说明里会**当场列出**当前支持的 `kind` 与走沙箱的 capability ——
那份清单由服务端广播,以工具说明为准,不要用本文件里的复述去覆盖它。

## 任务信封(cloud_dispatch)

- `autonomy: "pipeline"` —— 步骤可预先写死的任务。必填 `kind` + `input`。kind 目录:`research`(联网调研)、`code-review`、`docs`、`office-report`(docx/pptx/xlsx)、`data-analysis`(pandas/图表)、`bugfix`(patch+测试)、`migration`(多步重构)。
- `autonomy: "bounded-agent"` —— 开放式目标。必填 `objective` + `capabilities[]`(如 `["web_search"]`、`["code_exec"]`;声明能力而非 tier,平台自动路由运行时)。
- `budget`(可选,从紧):`{max_iter ≤50, max_tokens ≤500000, max_wall_clock_sec ≤1800}`。超预算的任务会被平台硬熔断,表现为**任务进入失败终态**(用户不会自己收到一个 HTTP 状态码 —— 云任务这条路上没有)。
- `output_schema`(可选):要结构化结果时给 JSON Schema。

## 计费语义(务必告知用户的部分)

- 云任务**恒计费**(平台代付模型)。六个工具里只有 `cloud_web_search` 在**调用入口**直接计费;
  其余五个入口不计费 —— 但 `cloud_dispatch` 派出去的任务在跑的时候按模型用量计,那不等于免费。
- 每次模型调用前平台预授权:额度尽就**不调模型**(不会产生费用)。
- 账单按 API key 归因,用户可在 https://codepuppy.cn/billing 查每笔用量。

## ⚠️ 额度用完时怎么说(最容易说错的一段)

**不要让用户去充值。** 平台的行为是已经定死的:订阅额度耗尽 ⇒ **默认硬拒,钱包不参与**,
所以**充值不会恢复服务**。能恢复的只有两条路,告诉用户这两条:

1. 等会员额度窗口重置;
2. 到账户页把「钱包溢出」开关显式打开,之后才会回落到钱包扣费。

两条路都不通(比如他既不想等也不想开开关)时,如实说「现在做不了」,不要暗示花钱能解决。

## 错误处理(先分清是哪条路)

平台有两条路,同一件事在两条路上长得不一样,别混着说:

**模型面**(用户把 `ANTHROPIC_BASE_URL` 指到平台、Claude Code 直接调模型):

- 401:ALPHA_API_KEY 无效/已撤销 → 让用户去 https://codepuppy.cn/keys 重新生成并更新环境变量。
- 402:额度不足 → **按上面那段说**(不是充值);不调模型,不产生费用。
- 403 lacks scope:key 缺对应 scope → 重新生成时勾上需要的那一项。
- 503:计费服务暂不可达(fail-closed 保护钱包)→ 稍后重试。

**云任务面**(`cloud_*` 这六个 MCP 工具):

- 连不上 / key 无效 / 缺 `cloud` scope:在**连接**这一层就被拒(401 / 403),
  提示同上 —— 重新生成 key 并勾选「云任务」。
- **派发本身不会因为额度不足被拒**(这个入口不计费)。额度是在任务**跑起来之后**
  耗尽的,表现为**任务进入失败终态**:用 `cloud_status` / `cloud_await` 会看到
  `status: "failed"` 加一段错误信息。把那段错误信息**原样呈现**给用户,
  如果它指向额度,按上面「额度用完时怎么说」那段解释。
- 工具调用被拒时,结果里带的是一个 JSON 对象,**没有 HTTP 状态码**可看 —— 别去找 402。

## 进度与产物

- `cloud_await`:有界等待(≤25s/次),长任务循环调用或改告知用户稍后查。
- `cloud_artifacts`:返回任务的 `result` 与产物**描述符**(id / 类型 / 大小 / 校验和 / 取回地址)。
  **产物字节永远不内联** —— 要拿到文件内容,得按描述符里的地址做一次带认证的下载。
- `cloud_cancel`:取消在跑的任务。已经结束的任务会如实回它的真实终态,**结果与产物保留**;
  取消受理后状态会先停在「取消中」,直到平台拿到「确实停了」的证据才落到「已取消」。
- 状态 phase 是中性的(routing / agent.running 等),没有更细的内部信息可查。
- 断开连接**不等于**取消:客户端断线任务照跑照计费,要停只能调 `cloud_cancel`。
