# App Layout

```
<app-name>/
├── src/
│   ├── bootstrap.ts                                # Module entry point and exports
│   ├── domain/
│   │   ├── business/                               # PURE business logic
│   │   │   └── <feature-name>/
│   │   │       ├── <feature-name>.mod.ts           # Feature exports
│   │   │       └── <feature-name>.test.ts          # Unit tests for pure logic
│   │   ├── data/                                   # NON-PURE data/integration logic
│   │   │   └── <feature-name>.mod.ts
│   │   └── coordinators/                           # Application layer orchestrators
│   │       └── <process-name>/
│   │           ├── <process-name>.mod.ts
│   │           └── <process-name>.test.ts          # this is an integration test
│   └── entrypoints/                                # Inbound boundary (controllers/pages/CLI)
│       └── <entrypoint-name>/                        # basic unit, but e2e tests instead
│            ├── mod.ts
│            └── test.ts                            # this is an e2e test
├── dto/
│   └── <dto-name>.ts
└── deno.json
```

# Polymorphic Pattern

Use when a feature has multiple implementations, for easy swap out

```
<feature-name>/
├── implementations/               # Concrete classes
│   ├── variant-a/
│   │   ├── variant-a.mod.ts
│   │   └── test.ts
│   └── variant-b/
│       ├── variant-b.mod.ts
│       └── test.ts
├── base/                        # Abstract class
│   ├── <feature-name>.base.ts
│   └── test.ts
│
└── <feature-name>.mod.ts
```
