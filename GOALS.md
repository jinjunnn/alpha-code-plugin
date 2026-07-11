# alpha-code-plugin Goals

> This document records durable product intent and repository boundaries. It is
> not a roadmap or status document. Active work is tracked in
> [repository Issues](https://github.com/jinjunnn/alpha-code-plugin/issues) and
> [Alpha Delivery](https://github.com/users/jinjunnn/projects/2).

## Mission

Provide the supported, minimal Claude Code entry point to Alpha cloud
capabilities without duplicating platform policy or business logic.

## Durable outcomes

### Thin, versioned integration

Success signals:

- MCP, command, and skill content maps to published Alpha contracts.
- Plugin behavior contains no independent job, billing, auth, or model-routing
  implementation.
- Contract drift fails validation before release.

### Reproducible and safe distribution

Success signals:

- Releases contain no credentials or environment-specific connection details.
- Installation, upgrade, rollback, and removal are documented and testable.
- Manifest, command, skill, and marketplace metadata are versioned together.

### Honest user experience

Success signals:

- Required scopes, billing behavior, data sent to cloud, and failure modes are
  explicit before use.
- Authentication directs users to `alpha-web`; enforcement and metering remain
  in `alpha-platform`.
- Unsupported or unavailable capabilities fail loudly without pretending the
  plugin completed work.

## Does not own

- Model gateway, cloud execution, metering, quota, or account services.
- Web identity, billing UX, payment, or Catalog authoring.
- Desktop application behavior.
- Cross-repository requirements, priority, status, or Sprint planning.
