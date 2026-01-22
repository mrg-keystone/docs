# Keystone LLM Reference

## Architecture

Three layers: **Business** (pure logic) → **Coordinators** (orchestration) → **Data** (external calls)

## Directory Structure

```
src/
├── bootstrap.ts
├── domain/
│   ├── business/<feature>/<feature>.mod.ts    # Pure, unit testable
│   ├── data/<feature>.mod.ts                  # DB/API calls, thin
│   └── coordinators/<process>/<process>.mod.ts # Orchestration
└── entrypoints/<name>/mod.ts                  # Controllers/routes/CLI
dto/<name>.ts                                  # Boundary objects
```

## Rules

- **Business**: Pure functions, NO external imports except `dto/`
- **Data**: External interactions only, keep thin, NO imports except `dto/`
- **Coordinators**: Sandwich pattern (get data → business logic → persist)
- **DTOs**: Use `class-validator` + `class-transformer` for validation

## Imports

```
@module/path  → internal imports
#package      → npm packages
fs            → node:fs (bare = node built-ins)
```

## Testing

| Layer | Test Type |
|-------|-----------|
| business/ | Unit |
| coordinators/ | Integration (mocked) |
| entrypoints/ | E2E |

## Patterns

```typescript
// Coordinator (sandwich pattern)
export async function createUser(input: UserDto) {
  const validated = validateUser(input);  // business
  return await saveUser(validated);        // data
}

// Business (pure)
export function validateUser(user: UserDto): UserDto {
  if (user.age < 0) throw new Error('Invalid age');
  return user;
}

// Data (thin wrapper)
export async function saveUser(user: UserDto) {
  return await db.users.create(user);
}
```

## Polymorphic Features

```
<feature>/
├── base/<feature>.base.ts
├── implementations/variant-a/variant-a.mod.ts
└── <feature>.mod.ts  # Barrel for active impl
```

Barrel exports only for: polymorphic `*.mod.ts` and `bootstrap.ts`
