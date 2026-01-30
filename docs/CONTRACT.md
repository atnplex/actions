# Governance Contract

This document defines the service level agreements, guarantees, and responsibilities for the `atnplex` actions ecosystem.

## 1. Guarantees

### Stability

- **Semantic Versioning**: All reusable workflows will be tagged with semantic versions (e.g., v1, v1.2, v1.2.3).
  - `v1`: Points to the latest stable release in the v1.x series. Non-breaking changes only.
  - `v1.2`: Points to the latest patch in the v1.2.x series.
  - `SHA`: Immutable reference for maximum security.
- **Breaking Changes**: Any breaking change will trigger a new major version (e.g., v2).

### Correctness

- **Linting**: All workflows are linted with `actionlint` before merge.
- **Security**: No secrets are stored in this repository. All workflows pass `CodeQL` analysis.

## 2. Consuming Repo Responsibilities

To use these workflows, a consuming repository MUST:

- **Provide Secrets**: Inherited secrets (`secrets: inherit`) or explicit secret passing is required. This repo holds no secrets.
- **Follow Standards**: Repositories must align with the directory structures expected by standard workflows (e.g., `src/` for source, `tests/` for tests) unless configured otherwise.

## 3. Deprecation Policy

- **Notice Period**: We pledge a 6-month deprecation notice for any supported workflow.
- **Communication**: Deprecations will be announced via GitHub Discussions and PR comments in consuming repos (via the `repo-audit` workflow).

## 4. Support

- **Issues**: Report bugs in the `actions` repository issue tracker.
- **Improvements**: PRs are welcome but must strictly adhere to `WORKFLOW-STANDARDS.md`.
