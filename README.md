# actions

Single Source of Truth (SSOT) for reusable GitHub Actions workflows, templates, automation standards, and governance across all atnplex repositories.

## 🎯 Purpose

This repository provides:

- **Governance & Standards** - Declarative enforcement via workflows
- **Reusable GitHub Actions workflows** for common CI/CD tasks
- **Repository templates** for consistent setup across the organization
- **Documentation** for workflow standards, AI collaboration, and runner strategy

## 🚀 Quick Start

### Using Reusable Workflows

Reference workflows from this repository in your workflow files:

```yaml
name: "PR Quality Gate"
on:
  pull_request:
    branches: [main]
jobs:
  lint:
    uses: atnplex/actions/.github/workflows/lint.yml@main
    with:
      language: python

  security:
    uses: atnplex/actions/.github/workflows/secret-scan.yml@main

  test:
    uses: atnplex/actions/.github/workflows/test.yml@main
```

### Applying Organization Standards

Standards are **declaratively enforced** via the [`repo-settings-enforcer.yml`](.github/workflows/repo-settings-enforcer.yml) workflow, which runs on a schedule and ensures all repositories match the baseline configuration.

**Baseline Configuration**: [`repo-settings-baseline.json`](.github/repo-settings-baseline.json)

For manual verification:

```bash
# Check current settings
gh api repos/atnplex/your-repo | jq .

# Compare against baseline
diff <(gh api repos/atnplex/your-repo | jq .) repo-settings-baseline.json
```

## 📁 Repository Structure

```
.
├── .github/
│   ├── workflows/              # Automation workflows
│   │   ├── repo-settings-enforcer.yml    # Declarative governance
│   │   ├── branch-protection-enforcer.yml
│   │   ├── lint.yml            # Reusable linting
│   │   ├── test.yml            # Reusable testing
│   │   ├── secret-scan.yml     # Security scanning
│   │   ├── docker-build.yml    # Docker builds
│   │   └── ...                 # 22 total workflows
│   └── repo-settings-baseline.json
├── .githooks/
│   ├── pre-commit              # Global pre-commit hook
│   └── pre-push                # Global pre-push hook (main branch checks)
├── docs/
│   ├── AI_COLLABORATION.md     # Cross-AI handoff guide
│   ├── CONTRACT.md             # Repository contract specifications
│   ├── GOVERNANCE_MATRIX.md    # Governance enforcement matrix
│   ├── ONBOARDING.md           # Adding new repos to SSOT
│   ├── RUNNER_STRATEGY.md      # Self-hosted runner taxonomy
│   ├── SECURITY-MODEL.md       # Security model documentation
│   └── WORKFLOW-STANDARDS.md   # Workflow standards
├── templates/
│   ├── ai/                     # AI artifact templates (.ai/ directory)
│   └── workflows/              # Workflow starter templates
├── workflow-packs/             # Bundled workflow packages
├── runners/                    # Runner bootstrap scripts
└── protection.json             # Branch protection baseline
```

## 🔧 Key Workflows

### Governance & Enforcement

- **[repo-settings-enforcer.yml](.github/workflows/repo-settings-enforcer.yml)** - Enforces repository settings across the org
- **[branch-protection-enforcer.yml](.github/workflows/branch-protection-enforcer.yml)** - Ensures branch protection rules
- **[repo-audit.yml](.github/workflows/repo-audit.yml)** - Audits repository compliance

### CI/CD & Quality

- **[lint.yml](.github/workflows/lint.yml)** - Multi-language linting
- **[test.yml](.github/workflows/test.yml)** - Configurable test execution
- **[secret-scan.yml](.github/workflows/secret-scan.yml)** - Secret scanning with Trivy
- **[docker-build.yml](.github/workflows/docker-build.yml)** - Multi-arch Docker builds
- **[format.yml](.github/workflows/format.yml)** - Code formatting checks

### Automation

- **[auto-merge.yml](.github/workflows/auto-merge.yml)** - Auto-merge approved PRs
- **[dependabot-auto-fix.yml](.github/workflows/dependabot-auto-fix.yml)** - Auto-fix Dependabot updates
- **[drift-detection.yml](.github/workflows/drift-detection.yml)** - Detect configuration drift

See [`.github/workflows/`](.github/workflows/) for all 22 workflows.

## 🖥️ Runner Strategy

All workflows support configurable runners with this preference hierarchy:

1. **Primary**: `[self-hosted, linux, amd64, tier:gold, cap:docker]` (Unraid server)
2. **Fallback**: `[self-hosted]`
3. **Last Resort**: `ubuntu-latest` (GitHub-hosted, limited quota)

**Governance**: Runners are registered at the organization level with security groups. Public repositories MUST NOT use `tier:gold` runners to prevent credential exfiltration.

See [docs/RUNNER_STRATEGY.md](docs/RUNNER_STRATEGY.md) for complete runner taxonomy and bootstrap instructions.

## 🤖 AI Collaboration

This repository enables seamless collaboration between multiple AI systems (Antigravity, GitHub Copilot, Jules.Google, Perplexity Pro) via standardized `.ai/` directory structures in all repositories.

**Key features**:

- Cross-AI state visibility
- Consistent handoff protocols
- Artifact templates for task tracking, implementation plans, and summaries

See [docs/AI_COLLABORATION.md](docs/AI_COLLABORATION.md) for the complete cross-AI collaboration guide.

## 📋 Organization Standards

All atnplex repositories follow these standards (enforced via `repo-settings-enforcer.yml`):

- ✅ **Default branch**: `main`
- ✅ **Branch protection**: Direct commits to `main` blocked
- ✅ **PR workflow**: All changes via branch → PR → merge
- ✅ **Auto-delete**: Merged branches auto-deleted
- ✅ **Auto-merge**: PRs auto-merge when checks pass (if configured)
- ✅ **Review required**: 1 approver minimum
- ✅ **Status checks**: Must pass before merge
- ✅ **Conversation resolution**: All comments must be resolved

See [docs/GOVERNANCE_MATRIX.md](docs/GOVERNANCE_MATRIX.md) for enforcement details.

## 📚 Documentation

- [AI Collaboration Guide](docs/AI_COLLABORATION.md) - Cross-AI handoff protocols
- [Contract Specifications](docs/CONTRACT.md) - Repository contract model
- [Governance Matrix](docs/GOVERNANCE_MATRIX.md) - Enforcement matrix
- [Onboarding Guide](docs/ONBOARDING.md) - Adding new repositories
- [Runner Strategy](docs/RUNNER_STRATEGY.md) - Self-hosted runner taxonomy and bootstrap
- [Security Model](docs/SECURITY-MODEL.md) - Security architecture
- [Workflow Standards](docs/WORKFLOW-STANDARDS.md) - Workflow conventions

## 🔄 Workflow

1. Create feature branch from `main`
2. Make changes
3. Open PR to `main`
4. Automated checks run (lint, security, test)
5. Request review (or auto-merge if configured)
6. Merge PR
7. Branch auto-deletes
8. Settings enforcer ensures compliance

## 🛠️ Maintenance

### Updating Workflows

1. Update workflows in this repository
2. Tag a new version: `git tag v2 && git push --tags`
3. Repos using `@main` get updates automatically
4. Repos using `@v1` stay pinned until manually updated

### Governance Updates

1. Update `repo-settings-baseline.json`
2. Merge to `main`
3. Enforcer workflow applies changes organization-wide

## 🔧 Global Git Hooks

This repository provides global Git hooks for all local repositories:

**Setup** (one-time):

```bash
git config --global core.hooksPath "C:/atn/github/actions/.githooks"
```

**Available Hooks**:

- **pre-commit**: Basic checks before commit
- **pre-push**: Heavy checks when pushing to `main` (tests, secret scanning)

**Escape Hatch**:

```bash
ATN_HOOKS_SKIP=1 git push  # Skip pre-push checks
```

## 🏗️ Migration from legacy-actions

This repository consolidates functionality from the deprecated `legacy-actions` repository:

- ✅ **Governance approach**: Migrated from imperative shell script to declarative YAML workflows
- ✅ **Documentation**: Ported and updated with current standards
- ✅ **Templates**: AI collaboration and workflow templates integrated
- ✅ **Runner strategy**: Bootstrap scripts and taxonomy preserved

The legacy repository served as SSOT for reusable workflows. This repository extends that model with comprehensive governance, enforcement, and AI collaboration capabilities.

## 📄 License

MIT

---

**Note**: This repository is the authoritative source for all atnplex organization standards, workflows, and governance policies.
