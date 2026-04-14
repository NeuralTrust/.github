# Contributing to NeuralTrust

Guidelines for all repositories in the NeuralTrust organization.

## Branching Model

| Branch | Purpose |
|--------|---------|
| `develop` | Integration branch — all feature work merges here first |
| `main` | Production branch — only receives merges from `develop` (or hotfixes) |
| `feature/*` | Short-lived feature branches off `develop` |
| `hotfix/*` | Emergency fixes applied directly to `main` |

## Merge Strategy

| PR Target | Merge Method | Why |
|-----------|-------------|-----|
| Feature → `develop` | **Squash merge** | Keeps develop history clean, one commit per feature |
| `develop` → `main` | **Merge commit** | Preserves commit graph so histories stay in sync |
| Hotfix → `main` | **Squash merge** | Single atomic fix on production |

> **Never squash merge `develop` → `main`.** This causes permanent history divergence —
> Git loses track of which commits are already merged, and future comparisons show ghost commits.

## Commit Messages & PR Titles

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]
```

Common types:

| Type | When to use | Semver impact |
|------|-------------|---------------|
| `feat` | New feature | **minor** |
| `fix` | Bug fix | **patch** |
| `refactor` | Code change that neither fixes a bug nor adds a feature | **patch** |
| `docs` | Documentation only | **patch** |
| `test` | Adding or updating tests | **patch** |
| `ci` | CI/CD pipeline changes | **patch** |
| `chore` | Maintenance (deps, configs, etc.) | **patch** |

For breaking changes, add `!` after the type/scope: `feat(api)!: remove v1 endpoints`  
This signals a **major** version bump.

Examples:
```
feat(api): add pagination to list endpoints
fix(auth): handle expired JWT tokens gracefully
ci(deps): bump actions/checkout from v3 to v4
refactor!: rename internal service interfaces
```

### Why This Is Enforced

Feature PRs are **squash-merged** into `develop`, so the **PR title becomes the commit message**.
Those commits feed directly into `ai-release-bump.yml`, which classifies the semver bump:

```
PR title: "feat(api): add pagination"
                ↓ squash merge into develop
commit: "feat(api): add pagination (#42)"
                ↓ merge develop → main
git log → AI release bump → v1.3.0 (MINOR)
```

## Pull Requests

### Creating a PR

1. Branch from `develop` (or `main` for hotfixes)
2. Keep PRs focused — one feature or fix per PR
3. **Title must follow Conventional Commits** — `type(scope): description`
4. **Fill in the Summary** in the PR body explaining **what** and **why**

### Review Process

PRs go through automated CI:

1. **Tests** — unit and integration tests
2. **SAST** — security scanning (Trivy is mandatory; Gitleaks/Gosec/Bandit/njsscan are informational)

### After Merge

- **Feature → develop**: The deploy pipeline builds and deploys to dev automatically
- **develop → main**: The auto-release pipeline creates a GitHub Release with AI-determined semver, which triggers the production release

## CI/CD Pipeline

See the [workflows repo README](https://github.com/NeuralTrust/workflows#readme) for full pipeline documentation including:

- Reusable workflow catalog
- Required secrets and variables
- Per-repo configuration examples
- Smoke test setup

## Security

- Never commit secrets, API keys, or credentials
- Use GitHub org secrets for sensitive values
- Trivy scans run on every PR and release — treat findings seriously
- Gitleaks checks for accidentally committed secrets

## Dependencies

- Dependabot runs weekly on `develop` for all repos
- Review and merge dependency PRs promptly
- Major version bumps are excluded by default — upgrade manually with testing
