---
description: 查询 Code Puppy 云任务状态与结果
---

用 MCP 工具 `cloud_status` 查询任务状态(参数 job_id: $ARGUMENTS)。
- 非终态(queued/running):报告当前 phase,建议稍后再查或用 `cloud_await` 等待。
- completed:用 `cloud_artifacts` 取回结果并呈现。
- failed/cancelled:把错误信息原样呈现。**如果失败原因指向额度用完,不要让用户去充值** ——
  平台对额度耗尽是硬拒、钱包不参与,充值不会恢复服务;能恢复的是「等会员额度窗口重置」
  或「到账户页把钱包溢出开关显式打开」。细节见 `alpha-cloud` skill 的「额度用完时怎么说」。
