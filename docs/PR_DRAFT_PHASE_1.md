# PR Description: Phase 1 - Foundation

## Summary

Establishes the foundational structure for the `atnplex/actions` Single Source of Truth (SSOT). This PR populates the repository with the initial set of **Reusable Workflows**, **Governance Documentation**, and **Protection Templates**. All workflows have been migrated from "workflow packs" to proper Reusable Workflows (`on: workflow_call`).

## Motivation

To centralize and standardize GitHub Actions across the `atnplex` organization. This SSOT repository will serve as the "supply chain" for all other repositories, ensuring:

- Consistent security scans (Secret Scan, CodeQL).
- Unified linting and formatting standards.
- Enforceable governance policies (Branch protection, "Clean" branches).

## Risks

- **Low**: This is an initial population of a new repository. No Production workflows depend on this yet.
- **Dependency Pinning**: External actions are pinned to `v3.82.7` (TruffleHog) or major versions to ensure stability.
- **Visibility**: The repository is public. We rely on Organization Settings Allowlisting to restrict usage to internal repos.

## Testing

- **Manual verification**: Verified workflow syntax and paths locally.
- **Dry Run**: The `branch-protection-enforcer` workflow is included but not yet scheduled/active.
- **Self-Validation**: Included `repo-bootstrap.yml` which references the temporary branch `@chore/init-ssot-actions` to allow for immediate testing upon merge.

## File Manifest (Phase 1)

### Reusable Workflows (`.github/workflows/`)

- `repo-bootstrap.yml`: The aggregator workflow.
- `lint.yml`: Actionlint, Shellcheck, Markdownlint.
- `format.yml`: Prettier with auto-commit.
- `secret-scan.yml`: TruffleHog (pinned to v3.82.7).
- `codeql.yml`: CodeQL analysis.
- `metadata.yml`: PR title validation.
- `enforce-clean.yml`: Checks for forbidden artifacts.
- `dependabot-auto-fix.yml`: Auto-merge logic for Dependabot.
- `branch-protection-enforcer.yml`: API wrapper for applying protection rules.
- (Plus other utility workflows: `archive-state`, `auto-merge`, `docker-build`, `drift-detection`, `repo-audit`, `repo-onboarding`, `test`)

### Templates (`templates/`)

- `bootstrap-workflow.yml`: The file to be copied to consuming repos.
- `protection-baseline.json`: Initial adoption rules (no status checks).
- `protection-strict.json`: Strict enforcement (required status checks).

### Documentation (`docs/`)

- `GOVERNANCE_MATRIX.md`: Definition of audit/fix strategies.
- `SECURITY-MODEL.md`: Auth and pinning strategy.
- `CONTRACT.md`, `ONBOARDING.md`, `WORKFLOW-STANDARDS.md`.
