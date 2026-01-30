# Onboarding Guide

How to adopt the `atnplex` rigorous governance model for your repository.

## 1. The One-File Setup

Create `.github/workflows/bootstrap.yml` in your repository:

```yaml
name: Bootstrap Governance

on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: "0 8 * * 1" # Weekly audit

jobs:
  bootstrap:
    uses: atnplex/actions/.github/workflows/repo-bootstrap.yml@v1
    secrets: inherit
```

This single file ensures:

- Branch protections are set.
- Required workflows are audited.
- Security scanners are active.

## 2. Using Specific Capabilities

To use specific capabilities, call them directly as Steps (Composite Actions) or Jobs (Reusable Workflows):

### As a Job (Reusable Workflow)

```yaml
jobs:
  security:
    uses: atnplex/actions/.github/workflows/codeql-analysis.yml@v1
```

### As a Step (Composite Action)

```yaml
steps:
  - uses: atnplex/actions/.github/actions/lint@v1
```

## 3. Verify Setup

1. Push the `bootstrap.yml` file.
2. Check the "Actions" tab in your repo.
3. If the bootstrap workflow identifies misalignment, it will open a Pull Request with fixes. Merge it!
