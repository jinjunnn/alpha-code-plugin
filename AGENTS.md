# Repository and development documentation

- This repository owns only Claude Code plugin packaging and integration.
- Track all active work in GitHub Issues and Alpha Delivery.
- Do not create local backlog, requirement-status, Issue-mirror, or Sprint files.
- Keep plugin contracts and release evidence durable and link them from the
  owning Issue or PR.
- Follow `jinjunnn/alpha-work/governance/delivery-standard.md` and
  `jinjunnn/alpha-work/governance/documentation-standard.md`.
- Use the shared `$maintain-repository-docs` skill during implementation,
  fixes, refactors, reviews, and releases with documentation impact.
- Treat runtime-loaded rules as protected execution assets. Documentation
  maintenance may index and validate them, but may not delete or rewrite them
  without explicit owner approval. `.remember/` is ignored local state with
  zero authority; do not commit it or delete user memory without a request.
