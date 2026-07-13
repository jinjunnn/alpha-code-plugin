# alpha-cloud — Claude Code plugin

把 [alpha 云平台](https://alphacodeone.com) 接进 Claude Code:**云任务派发**(research / 代码审查 / 文档 / 办公文档生成 / 数据分析 / bugfix / 迁移 + 开放式 bounded-agent)与**平台代付模型**。

本仓只拥有 Claude Code plugin 的 manifest、MCP/command/skill 包装、安装说明与
发布验证;模型网关、云任务实现、计量和账户逻辑归
[`alpha-platform`](https://github.com/jinjunnn/alpha-platform),API key 与账户体验归
[`alpha-web`](https://github.com/jinjunnn/alpha-web)。长期目标见
[`GOALS.md`](GOALS.md)。

## Delivery governance

- [Repository Issues](https://github.com/jinjunnn/alpha-code-plugin/issues)
- [Alpha Delivery](https://github.com/users/jinjunnn/projects/2)
- [Portfolio goals](https://github.com/jinjunnn/alpha-work/blob/main/GOALS.md)
- [Delivery standard](https://github.com/jinjunnn/alpha-work/blob/main/governance/delivery-standard.md)
- [Documentation contract](https://github.com/jinjunnn/alpha-work/blob/main/governance/documentation-standard.md)
- [Repository document index](docs/README.md)

GitHub Issues and Alpha Delivery are the operational source of truth. 本仓不维护
本地需求清单、状态或 Sprint 文档。

## 安装

```bash
# 1. 在 https://alphacodeone.com 登录 → 「API 密钥」页生成 key(勾选 models + cloud)
export ALPHA_API_KEY="sk-alpha-…"     # 写进你的 shell profile

# 2. 安装 plugin
claude plugin marketplace add jinjunnn/alpha-code-plugin
claude plugin install alpha-cloud
```

装好后获得:MCP 工具 `cloud_dispatch` / `cloud_status` / `cloud_await` / `cloud_artifacts` + 命令 `/alpha-cloud:dispatch`、`/alpha-cloud:status` + 用法 skill。

## 模型面(可选)

把 Claude Code 的模型调用切到平台代付(计费走你的 alpha 账户,会员窗口 → ¥钱包):

```bash
export ANTHROPIC_BASE_URL="https://alpha-gateway.tidelabs.click"
export ANTHROPIC_AUTH_TOKEN="$ALPHA_API_KEY"
```

其他 harness(Codex / Cursor / Cline / Zed / 通用 OpenAI SDK)接入见
[`alpha-platform` harness contract](https://github.com/jinjunnn/alpha-platform/blob/main/docs/contracts/harness.md)。

## 安全

- key 只存本机环境变量,插件配置经 `${ALPHA_API_KEY}` 展开,仓库内无任何明文。
- 泄露即刻在 portal 撤销(全网 ≤2 分钟生效)。
- 云任务受 per-job 硬预算熔断;每次模型调用先预授权,额度尽即拒、不产生费用。
