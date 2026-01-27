# Keystone

Keystone is MRG's foundation framework for building modular systems with clear boundaries between business logic, data access, and orchestration.

## Why Keystone Exists

- Keep "business" and "data" separated by design, not by convention
- Make orchestration explicit, so flows are testable and readable
- Standardize project structure so new services/modules are predictable

## Status

Keystone is under active development. APIs and conventions may change until v1.

## Suite Map

```
keystone-suite/
├── keystone/            # Backend specific logic (Danet)
├── keystone-ui/         # Frontend logic (Freshjs)
├── keystone-cli/        # Tools to make development easier
├── keystone-prototypes/ # Exploratory repo, items built here then moved to keystone
└── keystone-docs/       # Suite docs
```

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

## Testing

Tests must be flat and self-contained with no steps that depend on anything else. Each test should always run the same logic in isolation.
tests must not have loops. they must be explicit in their execution.

## Security and Configs

Keystone draws a hard line between **secrets** and **application configuration**.

### Secrets (Top-level `.env`)

- All secrets live in a single `.env` file at the **git root**
- This file is **never committed**
- It may contain credentials such as:

  - API keys (OpenAI, Stripe, etc.)
  - Database passwords
  - Service tokens

- Secrets are shared and synchronized via **Envault**, not duplicated per app

```
keystone-suite/
├── .env                # secrets only (gitignored)
├── keystone/
├── keystone-ui/
└── keystone-cli/
```

This ensures:

- one source of truth for sensitive values
- no secret sprawl across workspace members
- predictable secret loading in local, CI, and production environments

---

### Application Config (Workspace-level `.env`)

- Each workspace member **may** define its own `.env` file
- These files are for **non-sensitive configuration only**
- These files must have an "example-env" in the workspace root
- Examples:

  - ports
  - feature flags
  - log levels
  - environment-specific toggles

```
keystone/
├── deno.json
├── .env                # app config only (no secrets)
├── example-env
└── src/
```

---

### Rule of Thumb

- If leaking it would be _annoying_ → workspace `.env`
- If leaking it would be _expensive or dangerous_ → root `.env`

Secrets flow **downward** from the root.
Configs stay **local** to the app that owns them.

## External APIs

All external API integrations are tested and documented using [Bruno](https://www.usebruno.com/), an open source API client.

Each API collection is stored in its own folder under `external-apis/`:

```
external-apis/
├── bruno.json           # Root collection config
├── stripe/              # Stripe API requests
├── openai/              # OpenAI API requests
└── ...
```

This approach:

- Version controls API requests alongside documentation
- Provides reproducible API testing without proprietary cloud sync
- Keeps request collections portable and shareable

To use: open the `external-apis/` folder in Bruno.

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

## Environments and Secrets

The Keystone CLI syncs environment files from [Envault](https://github.com/envault/envault), a self-hosted .env sharing tool.

### Configure an environment

1. Log into [Envault](https://envault-deploy.ngrok.app)
2. Navigate to your app and select the environment
3. Copy the setup command shown under **"Set up this app"** and run it

The command is pre-filled with your environment details and includes a rotating key.

### Pull environment variables

```bash
keystone env pull <envName>
```

This pulls variables and saves them to a `.env` file in the current directory.

### List configured environments

```bash
keystone env list
```
