# Governance Coverage Matrix

This matrix defines the governance goals for the `atnplex` organization and how they are enforced by the `atnplex/actions` SSOT repository.

| Governance Goal                                               | Enforcement Type              | Automation Workflow                            | Permissions Required                 | Failure Mode / Fallback                                                                         |
| :------------------------------------------------------------ | :---------------------------- | :--------------------------------------------- | :----------------------------------- | :---------------------------------------------------------------------------------------------- |
| **Branch Protection** (Require PRs, Reviews, auto-resolution) | **Auto-Fix (API)**            | `branch-protection-enforcer.yml`               | `admin:org` or `repo` (Admin)        | Log error if token lacks scope; Report in Audit.                                                |
| **Required Status Checks** (Lint, Security, Test)             | **Auto-Fix (API)**            | `branch-protection-enforcer.yml` (Strict Mode) | `admin:org` or `repo` (Admin)        | Fails if check contexts haven't run yet (safe fail); Audit logs compliance gaps.                |
| **CI/Security Suite** (Clean, Lint, Secret Scan, CodeQL)      | **Auto-Fix (PR)**             | `repo-onboarding.yml` (adds `bootstrap.yml`)   | `workflow` (Write), `contents:write` | PR creation fails if branch exists; User manual merge required.                                 |
| **Dependabot Config** (`.github/dependabot.yml`)              | **Auto-Fix (PR)**             | `repo-onboarding.yml`                          | `contents:write`                     | Same as above.                                                                                  |
| **Repo Visibility / Privacy**                                 | **Audit Only**                | `repo-audit.yml`                               | `read:org`                           | Issue opened on `atnplex/actions` (or Alert) if public repo found unexpectedly.                 |
| **Fork Rules**                                                | **Audit Only**                | `repo-audit.yml`                               | `read:org`                           | Issue opened / Logged.                                                                          |
| **Actions Policy Allowlist** (Restrict to SSOT)               | **Audit Only** (Manual Setup) | N/A (One-off manual bootstrap)                 | `admin:org`                          | Documented in `SECURITY-MODEL.md`. Audit can only check local settings if accessible (limited). |
| **Force Pushes / Deletions**                                  | **Auto-Fix (API)**            | `branch-protection-enforcer.yml`               | `admin:org` or `repo` (Admin)        | API call fails; protection remains incomplete.                                                  |
| **"Allow GitHub Actions to create PRs"**                      | **Audit Only**                | `repo-audit.yml` (Check settings)              | `admin:org`                          | Logged in drift report; must be fixed in Org Settings.                                          |

## Automation Strategies

### 1. Repo Bootstrap (`repo-bootstrap.yml`)

- **Type**: Reusable Workflow (The "Standard")
- **Role**: The single entry point for all repositories. Calls lint, test, security, and cleaning workflows.
- **Deployment**: Added via PR by `repo-onboarding.yml`.

### 2. Repo Audit (`repo-audit.yml`)

- **Type**: Dispatch / Scheduled Workflow
- **Role**: Enumerates all org repos. Checks for:
  - Existence of `.github/workflows/bootstrap.yml`
  - Branch protection status (via API inspection)
  - Drift in critical files (`LICENSE`, `SECURITY.md`)
- **Action**: Triggers `repo-onboarding` for missing files; Triggers `branch-protection-enforcer` (if configured to auto-enforce).

### 3. Repo Onboarding (`repo-onboarding.yml`)

- **Type**: Dispatch Workflow
- **Role**: Corrective Automation. Checks out target repo, adds standard files (`bootstrap.yml`, `dependabot.yml`), and opens a PR.

### 4. Branch Protection Enforcer (`branch-protection-enforcer.yml`)

- **Type**: Reusable / Dispatch
- **Role**: Applies JSON protection schema via API.
- **Modes**:
  - `baseline`: Enforce admin reviews, conversation resolution. **No** status checks.
  - `strict`: Adds status checks (`lint`, `security-scan`) _only_ if determining they are available/required.

## Required Token Scopes

For the `ADMIN_TOKEN` used in enforcement workflows:

- `admin:org`: Required to list organization repositories and change organization-level settings.
- `repo` (or `admin:repo`): Required to change repository settings, branch protection, and merging strategies.
- `workflow`: Required to dispatch workflows and manage actions permissions.
- `delete_repo` (Optional): Only if we ever need to delete repos (not currently used).
