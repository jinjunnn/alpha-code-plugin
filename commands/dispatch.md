---
description: 派发一个云任务(pipeline 或 bounded-agent)到 alpha 云平台
---

把用户的任务派发到 alpha 云平台执行。

步骤:
1. 判断 autonomy:步骤能预先写死 → `pipeline`(kind ∈ research / code-review / docs / office-report / data-analysis / bugfix / migration);开放式目标 → `bounded-agent`(给 objective + capabilities)。
2. 调 MCP 工具 `cloud_dispatch`(信封字段:`autonomy`、pipeline 用 `kind`+`input`、bounded-agent 用 `objective`+`capabilities`、可选 `budget{max_iter,max_tokens,max_wall_clock_sec}`)。预算从紧:非必要不超默认。
3. 返回 job_id 后,用 `cloud_await` 等待(≤25s 轮询)或告知用户稍后用 `/alpha-cloud:status <job_id>` 查询。
4. 完成后用 `cloud_artifacts` 取结果,向用户呈现摘要 + 关键产物。

用户请求:$ARGUMENTS
