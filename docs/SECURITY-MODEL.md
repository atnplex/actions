# Security Model

This document explains the security architecture of the `atnplex` automation ecosystem.

## 1. Why Public?

The `actions` repository is public to leverage GitHub's open-source infrastructure:

- **Free Action Minutes**: Public repositories have unlimited Actions minutes.
- **Free Advanced Security**: CodeQL, Secret Scanning, and Dependabot are free for public repos.
- **Transparency**: Auditability is maximized.

## 2. Usage Restrictions

While the repository is public, the workflows are designed for internal `atnplex` use only.

- **Permissions Context**: Workflows typically run in the context of the _caller_ repository. If an external user tries to use our workflows, they will fail if they lack the specific strict internal secrets/contexts (though our design minimizes reliance on hidden secrets).
- **Explicit Checks**: Workflows may include a step to verify the `github.repository_owner` is `atnplex`.

### Org-Level Access Control (CRITICAL)

To prevent unauthorized usage of these reusable workflows, you MUST configure the Organization Settings:

1.  Go to **Organization Settings -> Actions -> General**.
2.  Under **Actions policies**, select **Allow <OWNER>, and select non-<OWNER>, actions and reusable workflows**.
3.  Add `atnplex/actions` to the allowlist.
4.  (Optional) For stricter control, disable "Allow access to public reusable workflows" if possible, or rely on the allowlist to block others.

This ensures that even though the repo is public, your internal repos are explicitly whitelisted to use it, while random external repos cannot easily rely on it if you restrict usage.

## 3. Secret Management

**THIS REPOSITORY CONTAINS NO SECRETS.**

- **Architecture**: This repo defines the _logic_ (the "How"). The consuming repos provide the _credentials_ (the "Keys").
- **Scanning**: Strict secret scanning is enabled. Any commit containing a secret will be rejected.

## 4. Integrity

- **Immutable Tags**: We recommend pinning to SHA for sensitive environments, but `v*` tags are protected branches in this repo.
- **Branch Protection**: Direct commits to `main` are disabled. All changes require PR review and passing status checks.
