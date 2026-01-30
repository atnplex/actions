# Runner Strategy & Taxonomy

## Overview

This document defines the taxonomy for labeling self-hosted GitHub Actions runners and governance policies for runner usage across the atnplex organization.

## Label Taxonomy

Runners are labeled using a tuple format to describe their capabilities. This allows workflows to target specific hardware constraints without knowing the hostname.

**Format**: `[os, arch, tier, capability...]`

### 1. OS & Architecture

- `linux` / `windows`
- `amd64` / `arm64`

### 2. Tier (Trust Level)

- `tier:gold`: Highly available, secure, fast IO. (e.g., dedicated Unraid server)
- `tier:silver`: Standard availability. (e.g., generic VPS)
- `tier:bronze`: Low priority / spot / desktop. (e.g., local WSL2 dev box)

### 3. Capabilities (Feature Flags)

- `cap:docker`: Can run Docker containers (has Docker socket access)
- `cap:gpu`: Has accessible GPU
- `cap:high-mem`: >16GB RAM
- `cap:fast-io`: NVMe / SSD storage

## Example Runner Labels

```yaml
# Unraid production server
runs-on: [self-hosted, linux, amd64, tier:gold, cap:docker, cap:fast-io]

# Development WSL2 machine
runs-on: [self-hosted, linux, amd64, tier:bronze, cap:docker]

# High-memory VPS
runs-on: [self-hosted, linux, amd64, tier:silver, cap:high-mem]

# GitHub-hosted fallback
runs-on: ubuntu-latest
```

## Governance Policy

### 1. Organization Level Registration

All runners are registered at the `atnplex` organization level, not per-repository. This ensures:

- Centralized management
- Consistent labeling
- Better resource utilization

### 2. Runner Groups

- **`default`**: Access to private repos only. Used for trusted code.
- **`public-safe`**: Ephemeral or sandboxed runners allowed for public repos.

### 3. Public Repository Security

**CRITICAL**: Public repositories MUST NOT run on `tier:gold` or `default` group runners to prevent credential exfiltration.

Public repos must use:

- `ubuntu-latest` (GitHub-hosted)
- Runners in the `public-safe` group (ephemeral containers only)

### 4. Fallback Strategy

Do not use fallback logic inside `runs-on` arrays. Runner selection should be configured by:

- Repository settings enforcer workflow
- Workflow templates
- Organization-level defaults

**Bad** (inconsistent):

```yaml
runs-on: ${{ matrix.os || 'ubuntu-latest' }}
```

**Good** (explicit):

```yaml
runs-on: [self-hosted, linux, amd64, tier:gold, cap:docker]
```

## Workflow Integration

### Preferred Runner Hierarchy

For most workflows, use this preference order:

1. **Primary**: `[self-hosted, linux, amd64, tier:gold, cap:docker]`
2. **Fallback**: `[self-hosted]`
3. **Last Resort**: `ubuntu-latest` (limited quota)

### Configurable Runner Input

Reusable workflows should accept a runner parameter:

```yaml
# .github/workflows/lint-reusable.yml
on:
  workflow_call:
    inputs:
      runner:
        description: "Runner label(s)"
        required: false
        type: string
        default: "[self-hosted, linux, amd64, tier:gold, cap:docker]"

jobs:
  lint:
    runs-on: ${{ fromJSON(inputs.runner) }}
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint
```

## Bootstrap

### Prerequisites

- Linux host (Ubuntu 22.04+ or Debian 12+ recommended)
- Docker installed (for containerized deployment)
- Systemd (for bare-metal deployment)
- Network access to `github.com`
- GitHub Personal Access Token with `admin:org` scope

### Deployment Options

#### Option 1: Docker Compose (Recommended)

```bash
cd runners/bootstrap
cp .env.example .env
# Edit .env with your GITHUB_TOKEN and ORG
docker-compose up -d
```

See [`runners/bootstrap/docker-compose.yml`](../runners/bootstrap/docker-compose.yml) for configuration.

#### Option 2: Systemd Service

```bash
cd runners/bootstrap
sudo ./install-systemd.sh \
  --token ghp_your_token \
  --org atnplex \
  --labels "self-hosted,linux,amd64,tier:gold,cap:docker"
```

See [`runners/bootstrap/install-systemd.sh`](../runners/bootstrap/install-systemd.sh) for details.

### Verification

After deployment, verify the runner appears in:

- GitHub → atnplex organization → Settings → Actions → Runners

Check labels match the intended taxonomy.

## Security Considerations

### Secrets & Credentials

- Runners in the `default` group have access to organization and repository secrets
- Never run untrusted code (e.g., public repo PRs) on `default` group runners
- Use ephemeral runners for public repositories

### Network Isolation

- `tier:gold` runners should be isolated on a secure network segment
- Restrict outbound access to necessary services (GitHub, Docker Hub, etc.)
- Use firewall rules to prevent lateral movement

### Updates & Patches

- Keep runner software updated (GitHub provides automatic updates)
- Regularly patch host OS and Docker runtime
- Monitor CVEs affecting self-hosted runners

## Maintenance

### Monitoring

Monitor runner health via:

- GitHub Actions UI (runner status)
- Host metrics (CPU, memory, disk)
- Docker logs (for containerized runners)

### Capacity Planning

Watch for:

- Queue times in workflow run logs
- Runner utilization patterns
- Workflow concurrency limits

Add runners when queue times consistently exceed 30 seconds.

### Decommissioning

To safely remove a runner:

1. Mark runner as offline in GitHub UI
2. Wait for in-progress jobs to complete
3. Stop the runner service
4. Remove runner from GitHub organization
5. Clean up host or container

## References

- [GitHub Actions: Self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners)
- [Self-hosted runner security](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#hardening-for-self-hosted-runners)
- [Runner groups](https://docs.github.com/en/actions/hosting-your-own-runners/managing-access-to-self-hosted-runners-using-groups)

---

For questions or improvements, open an issue in `atnplex/actions`.
