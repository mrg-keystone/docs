[syntax-highlighting]

Editor syntax highlighting for `requirements` files.

## Color Scheme

| Color | Elements                                                             |
| ----- | -------------------------------------------------------------------- |
| gold  | `[REQ]`, `[DTO]`, `[TYP]`, concrete tags, primitive types (`string`) |
| blue  | DTO references (`*Dto`)                                              |
| teal  | nouns and verbs (`noun.verb`)                                        |
| grey  | params, property names, TYP names                                    |
| green | boundary prefixes (`db:`, `ex:`, `os:`, etc.)                        |
| coral | faults (`!name`), brackets (`{}`, `[]`, `<>`)                        |

Grey is the default for identifiers. Blue overrides grey when the identifier ends in `Dto`. Gold overrides grey for primitive types.

## Tree-sitter Parser

### Node Types

The parser must produce these node types for highlights.scm to target:

| Node type         | Captures                                    | Color  |
| ----------------- | ------------------------------------------- | ------ |
| `req_tag`         | `[REQ]`                                     | purple |
| `concrete_tag`    | `[GENIE]`, `[FIVE_NINE]`                    | purple |
| `dto_reference`   | identifiers ending in `Dto`                 | purple |
| `signature`       | `noun.verb` as a unit                       | —      |
| `identifier`      | noun (before dot)                           | blue   |
| `method_name`     | verb (after dot)                            | blue   |
| `param_name`      | parameter names inside `()`                 | grey   |
| `type_name`       | return type identifiers                     | grey   |
| `property_name`   | prop names inside `{}`                      | grey   |
| `boundary_prefix` | `db:`, `ex:`, `os:`, `fs:`, `mq:`, `lg:`    | green  |
| `fault_line`      | container for one or more faults            | —      |
| `fault_marker`    | `!` prefix                                  | coral  |
| `fault_name`      | `not-found`, `timeout`, etc. after `!`      | coral  |
| `inline_dto`      | region inside `{}`                          | —      |
| `dto_tag`         | `[DTO]`                                     | gold   |
| `dto_def_name`    | DTO name after `[DTO]` tag                  | blue   |
| `typ_tag`         | `[TYP]`                                     | gold   |
| `typ_name`        | name before `:`                             | grey   |
| `typ_type`        | type after `:`                              | gold   |
| `typ_array_type`  | array type like `UrlDto[]`                  | gold   |
| `typ_generic_type`| generic like `Record<K, V>`                 | gold   |

### Punctuation

- `{` `}`, `[` `]`, `<` `>`, `(`, `)`, `:`, `,`, `|` → coral

### Treesitter Capture Groups

| Node type         | Capture                | Nvim highlight group |
| ----------------- | ---------------------- | -------------------- |
| `req_tag`         | `@keyword`             | Keyword              |
| `concrete_tag`    | `@keyword`             | Keyword              |
| `tag_name`        | `@keyword`             | Keyword              |
| `dto_reference`   | `@type.builtin`        | type.builtin         |
| `identifier`      | `@function`            | Function             |
| `method_name`     | `@function`            | Function             |
| `param_name`      | `@variable.parameter`  | variable.parameter   |
| `property_name`   | `@variable.parameter`  | variable.parameter   |
| `type_name`       | `@variable.parameter`  | variable.parameter   |
| `boundary_prefix` | `@keyword.modifier`    | Keyword              |
| `fault_marker`    | `@punctuation.special` | Special              |
| `fault_name`      | `@error`               | Error                |
| `dto_tag`         | `@keyword`             | Keyword              |
| `dto_def_name`    | `@type.builtin`        | type.builtin         |
| `typ_tag`         | `@keyword`             | Keyword              |
| `typ_name`        | `@variable.parameter`  | variable.parameter   |
| `typ_type`        | `@type`                | Type                 |
| `typ_array_type`  | `@punctuation.special` | Special (brackets)   |
| `typ_generic_type`| `@punctuation.special` | Special (brackets)   |
| `{}` `[]` `<>`    | `@punctuation.special` | Special              |

### Expected AST

Based on `./requirements`:

Line 1: `[REQ] recording.set(GetRecordingDto): RecordingSetResponseDto`

```
req_line
├── req_tag "[REQ]"                          → purple
├── signature
│   ├── identifier "recording"               → blue
│   └── method_name "set"                    → blue
├── parameters
│   └── dto_reference "GetRecordingDto"      → purple
└── return_type
    └── dto_reference "RecordingSetResponseDto" → purple
```

Line 2: `    id.create(providerName, externalId): internalId`

```
step_line
├── signature
│   ├── identifier "id"                      → blue
│   └── method_name "create"                 → blue
├── parameters
│   ├── param_name "providerName"            → grey
│   └── param_name "externalId"              → grey
└── return_type
    └── type_name "internalId"               → grey
```

Line 3: `      !not-valid-provider`

```
fault_line
└── fault
    ├── fault_marker "!"                     → coral
    └── fault_name "not-valid-provider"      → coral
```

Line 7: `      !not-found !timed-out` (multi-fault line)

```
fault_line
├── fault
│   ├── fault_marker "!"                     → coral
│   └── fault_name "not-found"               → coral
└── fault
    ├── fault_marker "!"                     → coral
    └── fault_name "timed-out"               → coral
```

Line 5: `    [GENIE]`

```
concrete_tag "[GENIE]"                       → purple
```

Line 6: Boundary step with DTO reference

```
    ex:provider.search(GenieCredentialsDto, externalId): search
```

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "search"                 → teal
├── parameters
│   ├── dto_reference "GenieCredentialsDto"  → blue
│   └── param_name "externalId"              → grey
└── return_type
    └── type_name "search"                   → grey
```

Lines 52-55: DTO definition block

```
[DTO] GenieCredentialsDto {
  genieAcctId: string, genieAcctPass: string,
  providerName: string, externalRecordingId: string,
}
```

```
dto_definition
├── dto_tag "[DTO]"                          → gold
├── dto_def_name "GenieCredentialsDto"       → blue
├── "{"                                      → coral
├── dto_property
│   ├── property_name "genieAcctId"          → grey
│   └── type_name "string"                   → gold
├── dto_property
│   ├── property_name "genieAcctPass"        → grey
│   └── type_name "string"                   → gold
├── dto_property
│   ├── property_name "providerName"         → grey
│   └── type_name "string"                   → gold
├── dto_property
│   ├── property_name "externalRecordingId"  → grey
│   └── type_name "string"                   → gold
└── "}"                                      → coral
```

Line 9: `    ex:provider.download(UrlDto): BinaryData`

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → blue
│   └── method_name "download"               → blue
├── parameters
│   └── dto_reference "UrlDto"               → purple
└── return_type
    └── type_name "BinaryData"               → grey
```

### Multi-line Steps

Steps can span multiple lines when parameters are long. The parser tracks parentheses depth to continue parsing until the closing `)`:

```
    os:storage.save(
        internalRecordingId,
        recordingData: boolean
    ): void
```

The LSP parser emits `MultilineContinuation` for continuation lines, preserving expected indentation from the opening line.

---

Line 11: `    db:metadata.set(internalId, metadata): void`

```
boundary_line
├── boundary_prefix "db:"                    → green
├── signature
│   ├── identifier "metadata"                → blue
│   └── method_name "set"                    → blue
├── parameters
│   ├── param_name "internalId"              → grey
│   └── param_name "metadata"                → grey
└── return_type
    └── type_name "void"                     → grey
```

Line 38: Type definition with array type

```
[TYP] search: UrlDto[]
```

```
typ_definition
├── typ_tag "[TYP]"                          → gold
├── typ_name "search"                        → grey
└── typ_type
    └── typ_array_type
        ├── dto_reference "UrlDto"           → blue
        ├── "["                              → coral
        └── "]"                              → coral
```

Line 43: Type definition with generic type

```
[TYP] metadata: Record<string, Primitive>
```

```
typ_definition
├── typ_tag "[TYP]"                          → gold
├── typ_name "metadata"                      → grey
└── typ_type
    └── typ_generic_type
        ├── type_name "Record"               → gold
        ├── "<"                              → coral
        ├── type_name "string"               → gold
        ├── type_name "Primitive"            → gold
        └── ">"                              → coral
```
