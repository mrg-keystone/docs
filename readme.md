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

## Quick Start

### Install the CLI

### Setup

## Environments and Secrets

You can use the Keystone CLI to sync your environment files where you can find any secrets you need.
