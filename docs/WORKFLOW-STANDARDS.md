# Workflow Standards

All workflows in `workflow-packs/` must adhere to these standards.

## 1. Naming Conventions

- **File Names**: `kebab-case.yml` (e.g., `pr-auto-fix.yml`).
- **Job IDs**: `kebab-case` (e.g., `lint-code`, `deploy-staging`).
- **Step Names**: Clear, descriptive, present tense (e.g., "Cache dependencies", not "Caching").

## 2. Structure

### Atomic Design

Workflows should be "atomic" — doing one thing well.

- **Bad**: `build-test-deploy.yml`
- **Good**: `build.yml`, `test.yml`, `deploy.yml` (chained via `workflow_call` if needed).

### Inputs

- **Explicit Types**: Always specify `type`, `description`, and `required` for inputs.
- **Defaults**: Provide sensible defaults where safe.

```yaml
inputs:
  node-version:
    description: "Node.js version to use"
    type: string
    default: "20"
    required: false
```

## 3. Corrective Action

Prefer "Fixing" over "Complaining".

- If lint fails -> Attempt `eslint --fix` and push (if safe).
- If config is wrong -> Open a PR to fix it automatically.

## 4. Documentation

Each workflow file must start with a comment block explaining:

- What it does.
- What inputs it expects.
- What permissions it needs.
