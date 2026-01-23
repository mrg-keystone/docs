# Architecture

## Directory Structure

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

---

## Logic Classification

Logic is classified into two camps based on testability:

### Business Logic

Pure application logic. Cannot have imports from outside of the `dto/` folder. This is where domain rules live and can be unit tested in isolation.

### Data Logic

Impure functionality that handles external interactions. When extracting this from prototypes, keep it as thin as possible. Cannot have imports outside of the `dto/` folder. This is the **outbound boundary** of the application.

### Coordinators

Coordinators join business and data logic using a sandwich pattern:

1. **Outer function** (data) — handles setup, fetching data, and side effects
2. **Inner function** (business) — defines the pure business logic

The flow follows: get data → pass to business logic → persist results or make external calls.

---

## Boundaries

### DTOs

Data Transfer Objects are the boundaries. They use `class-validator` and `class-transformer` libraries to enforce runtime type safety and provide IDE types.

### Entrypoints

The **inbound boundary** where users interact with the application. This can be controllers, routes, or exports.

### Barrel Exports

Only use barrel exports on:
- Polymorphic `<feature-name>.mod.ts` files
- `bootstrap.ts` files

---

## Testing

| Layer           | Test Type   | Purpose                                  |
| --------------- | ----------- | ---------------------------------------- |
| `business/`     | Unit        | Test pure logic in isolation             |
| `coordinators/` | Integration | Test orchestration with mocked externals |
| `entrypoints/`  | E2E         | Test full cycle                          |

---

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
