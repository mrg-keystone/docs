# App Layout

Standard directory structure for Keystone applications:

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

## Test Types by Layer

| Layer           | Test Type   | Purpose                                  |
| --------------- | ----------- | ---------------------------------------- |
| `business/`     | Unit        | Test pure logic in isolation             |
| `coordinators/` | Integration | Test orchestration with mocked externals |
| `entrypoints/`  | E2E         | Test full cycle                          |

## Polymorphic Pattern

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
