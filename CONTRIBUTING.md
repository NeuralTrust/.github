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

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]
```

Common types:

| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `docs` | Documentation only |
| `test` | Adding or updating tests |
| `ci` | CI/CD pipeline changes |
| `chore` | Maintenance (deps, configs, etc.) |

Examples:
```
feat(api): add pagination to list endpoints
fix(auth): handle expired JWT tokens gracefully
ci(deps): bump actions/checkout from v3 to v4
```

> Commit messages matter — our AI release workflow uses them to determine the semver bump
> (major/minor/patch). Clear, conventional messages lead to accurate versioning.

## Pull Requests

### Creating a PR

1. Branch from `develop` (or `main` for hotfixes)
2. Keep PRs focused — one feature or fix per PR
3. Write a clear title following conventional commits format
4. Add a description explaining **what** and **why**

### Review Process

PRs to `main` go through automated CI:

1. **AI Code Review** — automated review with inline comments
2. **Tests** — unit and integration tests
3. **SAST** — security scanning (Trivy is mandatory; Gitleaks/Gosec/Bandit/njsscan are informational)
4. **Auto-Approve** — if all checks pass, the PR is automatically approved

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
