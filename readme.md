# Keystone

Keystone is MRG’s foundation framework for building modular systems with clear boundaries between business logic, data access, and orchestration.

# Why Keystone exists

- Keep “business” and “data” separated by design, not by convention.
- Make orchestration explicit, so flows are testable and readable.
- Standardize project structure so new services/modules are predictable.

# Status

Keystone is under active development. APIs and conventions may change until v1.

# Suite Map

```
keystone-suite/
├── keystone/            # Backend specific logic (Danet)
├── keystone-ui/         # Frontend Logic (Freshjs)
├── keystone-cli/        # Tools to make development easier
├── keystone-prototypes/ # Exploratory repo, items built here then moved to keystone
└── keystone-docs/       # Suite Docs
```

# Imports

- Use `import_map` in `deno.json` for all imports
- Prefer absolute aliases for cross-domain references
- Use relative imports within polymorphic features

## Aliases

    - internal imports are prefixed with "@"
    - external imports are prefixed with "#"
    - all node imports are bare imports

## Examples

    - @<module-name>/dto
    - fs (for node:fs)
    - #date-fns (for npm:date-fns)

## Testing

tests are always to be flat with no steps that depend on anything else
meaning they are self contained and always run the same logic.

# Quick start

## Install the cli

## Setup

# Environments and secrets

You can use the keystone cli to sync your environment files where you can find
any secrets you need
