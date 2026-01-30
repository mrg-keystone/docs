# Keystone Suite Documentation

> Source: docs/readme.md, docs/technical/*.md
> Generated for LLM consumption

---

## Overview

Keystone is MRG's foundation framework for building modular systems with clear boundaries between business logic, data access, and orchestration.

### Why Keystone Exists

- Keep "business" and "data" separated by design, not by convention
- Make orchestration explicit, so flows are testable and readable
- Standardize project structure so new services/modules are predictable

### Status

Keystone is under active development. APIs and conventions may change until v1.

### Suite Map

```
keystone-suite/
├── keystone/            # Backend specific logic (Danet)
├── keystone-ui/         # Frontend logic (Freshjs)
├── keystone-cli/        # Tools to make development easier
├── keystone-prototypes/ # Exploratory repo, items built here then moved to keystone
└── keystone-docs/       # Suite docs
```

---

## Imports

- Use `import_map` in `deno.json` for all imports
- Prefer absolute aliases for cross-domain references
- Use relative imports within polymorphic features

### Aliases

| Prefix | Type                 | Example              |
| ------ | -------------------- | -------------------- |
| `@`    | Internal imports     | `@<module-name>/dto` |
| `#`    | External npm imports | `#date-fns`          |
| (bare) | Node built-ins       | `fs` (for `node:fs`) |

---

## Architecture

> Source: docs/technical/architecture.md

### Directory Structure

```
<app-name>/
├── src/
│   ├── bootstrap.ts                    # Module entry point and exports
│   ├── domain/
│   │   ├── business/                   # Pure business logic
│   │   │   └── <feature-name>/
│   │   │       ├── <feature-name>.mod.ts
│   │   │       └── <feature-name>.test.ts    # Unit tests
│   │   ├── data/                       # Impure data/integration logic
│   │   │   └── <feature-name>.mod.ts
│   │   └── coordinators/               # Application layer orchestrators
│   │       └── <process-name>/
│   │           ├── <process-name>.mod.ts
│   │           └── <process-name>.test.ts    # Integration tests
│   └── entrypoints/                    # Inbound boundary (controllers/pages/CLI)
│       └── <entrypoint-name>/
│           ├── mod.ts
│           └── test.ts                 # E2E tests
├── dto/
│   └── <dto-name>.ts
└── deno.json
```

### Logic Classification

Logic is classified into two camps based on testability:

**Business Logic**: Pure application logic. Cannot have imports from outside of the `dto/` folder. This is where domain rules live and can be unit tested in isolation.

**Data Logic**: Impure functionality that handles external interactions. When extracting this from prototypes, keep it as thin as possible. Cannot have imports outside of the `dto/` folder. This is the outbound boundary of the application.

**Coordinators**: Join business and data logic using a sandwich pattern:
1. Outer function (data) — handles setup, fetching data, and side effects
2. Inner function (business) — defines the pure business logic

The flow follows: get data → pass to business logic → persist results or make external calls.

### Boundaries

**DTOs**: Data Transfer Objects are the boundaries. They use `class-validator` and `class-transformer` libraries to enforce runtime type safety and provide IDE types.

**Entrypoints**: The inbound boundary where users interact with the application. This can be controllers, routes, or exports.

**Barrel Exports**: Only use barrel exports on:
- Polymorphic `<feature-name>.mod.ts` files
- `bootstrap.ts` files

### Polymorphic Pattern

Use when a feature has multiple implementations that can be swapped:

```
<feature-name>/
├── base/                           # Abstract class
│   ├── <feature-name>.base.ts
│   └── test.ts
├── implementations/                # Concrete classes
│   ├── variant-a/
│   │   ├── variant-a.mod.ts
│   │   └── test.ts
│   └── variant-b/
│       ├── variant-b.mod.ts
│       └── test.ts
└── <feature-name>.mod.ts           # Barrel export for active implementation
```

---

## Testing

Tests must be flat and self-contained with no steps that depend on anything else. Each test should always run the same logic in isolation. Tests must not have loops. They must be explicit in their execution.

| Layer           | Test Type   | Purpose                                  |
| --------------- | ----------- | ---------------------------------------- |
| `business/`     | Unit        | Test pure logic in isolation             |
| `coordinators/` | Integration | Test orchestration with mocked externals |
| `entrypoints/`  | E2E         | Test full cycle                          |

---

## Infrastructure

> Source: docs/technical/infrastructure.md

### Runtime

All code runs on **Deno** with **TypeScript**. No Node.js, no Bun.

### Frameworks

| Layer    | Framework    |
| -------- | ------------ |
| Frontend | Deno Fresh   |
| Backend  | Danet        |

### Hosting

**Primary: Deno Deploy**

All applications are hosted on Deno Deploy by default.
- Edge-native, globally distributed
- Zero-config deployments from git
- Native Deno support

**Supplementary Services**

| Need              | Service        |
| ----------------- | -------------- |
| Object storage    | AWS S3         |
| AI/ML inference   | Amazon Bedrock |
| Relational DB     | Supabase       |
| Background jobs   | External queue |

Keep supplementary services minimal. Prefer Deno Deploy-native solutions when available.

### Git Strategy

| Branch      | Purpose                          | Protected |
| ----------- | -------------------------------- | --------- |
| `main`      | Production, auto-deploys         | Yes       |
| `develop`   | Staging and integration testing  | Yes       |
| `feature/*` | New features                     | No        |
| `hotfix/*`  | Urgent production fixes          | No        |

**Flow**:
```
feature/xyz ──┐
              ├──► develop ──► main
feature/abc ──┘
```

1. Create feature branches from `develop`
2. Open PR to merge into `develop`
3. Test and validate on staging
4. PR from `develop` into `main` for release

**Deployments**:
- `main`: Deno Deploy watches this branch. Every commit triggers production deployment.
- `develop`: Used for staging environment and integration testing.

**Hotfixes**:
Branches prefixed with `hotfix/` trigger automation:
```
hotfix/critical-bug ──► develop ──► main (automated)
```

**Rules**:
- Never commit directly to `main` or `develop`
- All changes go through PRs
- Feature branches must be up-to-date with `develop` before merge
- Delete branches after merge

---

## Security and Configs

Keystone draws a hard line between **secrets** and **application configuration**.

### Secrets (Top-level `.env`)

- All secrets live in a single `.env` file at the git root
- This file is never committed
- Contains credentials such as API keys, database passwords, service tokens
- Secrets are shared and synchronized via Envault

```
keystone-suite/
├── .env                # secrets only (gitignored)
├── keystone/
├── keystone-ui/
└── keystone-cli/
```

### Application Config (Workspace-level `.env`)

- Each workspace member may define its own `.env` file
- These files are for non-sensitive configuration only
- These files must have an "example-env" in the workspace root
- Examples: ports, feature flags, log levels, environment-specific toggles

### Rule of Thumb

- If leaking it would be annoying → workspace `.env`
- If leaking it would be expensive or dangerous → root `.env`

Secrets flow downward from the root. Configs stay local to the app that owns them.

---

## External APIs

All external API integrations are tested and documented using Bruno, an open source API client.

Each API gets its own Bruno collection under `external-apis/`:

```
external-apis/
├── stripe/
│   └── bruno.json
├── openai/
│   └── bruno.json
└── ...
```

This approach:
- Version controls API requests alongside documentation
- Provides reproducible API testing without proprietary cloud sync
- Keeps request collections portable and shareable

---

## Quick Start

### Install the CLI

```bash
npm install -g @mrg-keystone/cli
```

### Setup

Clone all keystone-suite repositories:

```bash
keystone init [path]
```

This will clone all repositories into a `keystone-suite` directory at the specified path (defaults to current directory).

### Environments and Secrets

The Keystone CLI syncs environment files from Envault, a self-hosted .env sharing tool.

**Configure an environment**:
1. Log into Envault
2. Navigate to your app and select the environment
3. Copy the setup command shown under "Set up this app" and run it

**Pull environment variables**:
```bash
keystone env pull <envName>
```

**List configured environments**:
```bash
keystone env list
```
