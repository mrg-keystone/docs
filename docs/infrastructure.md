# Tech Stack & Hosting

## Runtime

All code runs on **Deno** with **TypeScript**. No Node.js, no Bun.

## Frameworks

| Layer    | Framework    |
| -------- | ------------ |
| Frontend | Deno Fresh   |
| Backend  | Danet        |

## Hosting

### Primary: Deno Deploy

All applications are hosted on **Deno Deploy** by default.

- Edge-native, globally distributed
- Zero-config deployments from git
- Native Deno support

### Supplementary Services

When Deno Deploy lacks a capability, use external services:

| Need              | Service        |
| ----------------- | -------------- |
| Object storage    | AWS S3         |
| AI/ML inference   | Amazon Bedrock |
| Relational DB     | Supabase       |
| Background jobs   | External queue |

Keep supplementary services minimal. Prefer Deno Deploy-native solutions when available.

---

## Git Strategy

### Branches

| Branch      | Purpose                          | Protected |
| ----------- | -------------------------------- | --------- |
| `main`      | Production, auto-deploys         | Yes       |
| `develop`   | Staging and integration testing  | Yes       |
| `feature/*` | New features                     | No        |
| `hotfix/*`  | Urgent production fixes          | No        |

### Flow

```
feature/xyz ──┐
              ├──► develop ──► main
feature/abc ──┘
```

1. Create feature branches from `develop`
2. Open PR to merge into `develop`
3. Test and validate on staging
4. PR from `develop` into `main` for release

### Deployments

- **main**: Deno Deploy watches this branch. Every commit triggers production deployment.
- **develop**: Used for staging environment and integration testing.

### Hotfixes

Branches prefixed with `hotfix/` trigger automation:

```
hotfix/critical-bug ──► develop ──► main (automated)
```

1. Create `hotfix/` branch from `develop`
2. Fix the issue
3. Merge into `develop`
4. Automation merges `develop` into `main` immediately

This bypasses the normal release cycle for urgent production issues.

### Rules

- Never commit directly to `main` or `develop`
- All changes go through PRs
- Feature branches must be up-to-date with `develop` before merge
- Delete branches after merge
