---
description: 查询 alpha 云任务状态与结果
---

用 MCP 工具 `cloud_status` 查询任务状态(参数 job_id: $ARGUMENTS)。
- 非终态(queued/running):报告当前 phase,建议稍后再查或用 `cloud_await` 等待。
- completed:用 `cloud_artifacts` 取回结果并呈现。
- failed/cancelled:呈现错误信息;如果是 402 额度类错误,提示用户到 alphacodeone.com 充值。
