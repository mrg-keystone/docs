[syntax-highlighting]

Editor syntax highlighting for `requirements` files.

## Color Scheme

| Color | Elements                                                                    |
| ----- | --------------------------------------------------------------------------- |
| gold  | `[REQ]`, `[DTO]`, `[TYP]`, concrete tags, `--` poly markers, `return`, primitive types |
| blue  | DTO references (`*Dto`)                                              |
| teal  | nouns and verbs (`noun.verb`)                                        |
| grey  | params, property names, TYP names, type references in DTOs, comments |
| green | boundary prefixes (`db:`, `ex:`, `os:`, etc.)                        |
| coral | faults (`!name`), brackets (`{}`, `[]` tuples, `<>`)                 |

Grey is the default for identifiers. Blue overrides grey when the identifier ends in `Dto`. Gold overrides grey for primitive types in `[TYP]` definitions.

## Tree-sitter Parser

### Node Types

The parser must produce these node types for highlights.scm to target:

| Node type         | Captures                                    | Color  |
| ----------------- | ------------------------------------------- | ------ |
| `req_tag`         | `[REQ]`                                     | purple |
| `concrete_tag`    | `[GENIE]`, `[FIVE_NINE]`                    | purple |
| `dto_reference`   | identifiers ending in `Dto`                 | purple |
| `signature`       | `noun.verb` or `Noun::verb` as a unit       | —      |
| `static_marker`   | `::` for static methods                     | teal   |
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
| `dto_array_prop`  | array property base, e.g. `url` in `url(s)` | grey   |
| `dto_array_suffix`| plural suffix `(s)`, `(es)`, `(ren)`        | coral  |
| `typ_tag`         | `[TYP]`                                     | gold   |
| `typ_name`        | name before `:`                             | grey   |
| `typ_type`        | type after `:`                              | gold   |
| `typ_generic_type`| generic like `Array<url>`, `Record<K, V>`   | gold   |
| `typ_tuple_type`  | tuple like `[id, name]`                     | gold   |
| `poly_marker`     | `--` polymorphic block delimiter            | gold   |
| `return_keyword`  | `return` in `return(value)`                 | gold   |
| `return_step`     | `return(value)` built-in step               | —      |
| `cotr_keyword`    | `cotr` keyword in constructor shorthand     | gold   |
| `cotr_step`       | `class::cotr` constructor step              | —      |
| `comment`         | `// text` inline comments                   | grey   |

### Punctuation

- `{` `}`, `[` `]`, `<` `>`, `(`, `)`, `:`, `::`, `,`, `|` → coral

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
| `dto_array_prop`  | `@variable.parameter`  | variable.parameter   |
| `dto_array_suffix`| `@punctuation.special` | Special              |
| `typ_tag`         | `@keyword`             | Keyword              |
| `typ_name`        | `@variable.parameter`  | variable.parameter   |
| `typ_type`        | `@type`                | Type                 |
| `typ_generic_type`| `@punctuation.special` | Special (brackets)   |
| `typ_tuple_type`  | `@punctuation.special` | Special (brackets)   |
| `poly_marker`     | `@keyword`             | Keyword              |
| `return_keyword`  | `@keyword`             | Keyword              |
| `cotr_keyword`    | `@keyword`             | Keyword              |
| `comment`         | `@comment`             | Comment              |
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

Line 15: `    metadata::cotr` (constructor shorthand)

```
cotr_step
├── identifier "metadata"                    → blue
├── static_marker "::"                       → teal
└── cotr_keyword "cotr"                      → gold
```

- No parentheses or return type
- Implicitly returns the class type
- `cotr()` or `cotr(): type` are parse errors

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

Lines 6-14: Polymorphic block

```
    provider.getRecording(externalId): data       ← 4 spaces
      --                                          ← 6 spaces
      [GENIE]                                     ← 6 spaces
      ex:provider.search(externalId): SearchDto   ← 6 spaces
        !not-found !timed-out                     ← 8 spaces
      ex:provider.download(UrlDto): DataDto       ← 6 spaces
        !not-found !timed-out                     ← 8 spaces
      --                                          ← 6 spaces
```

```
step_line
├── signature
│   ├── identifier "provider"               → teal
│   └── method_name "getRecording"          → teal
├── parameters
│   └── param_name "externalId"             → grey
└── return_type
    └── type_name "data"                    → grey

poly_marker "--"                             → gold

concrete_tag "[GENIE]"                       → gold

boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "search"                 → teal
...

poly_marker "--"                             → gold
```

Line 8: `    [GENIE]`

```
concrete_tag "[GENIE]"                       → gold
```

Line 9: Boundary step with DTO reference

```
    ex:provider.search(externalId): SearchDto
```

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "search"                 → teal
├── parameters
│   └── param_name "externalId"              → grey
└── return_type
    └── dto_reference "SearchDto"            → blue
```

Lines 71-74: DTO definition block

```
[DTO] GenieCredentialsDto {
  id, pass,
  provider, externalId,
}
```

```
dto_definition
├── dto_tag "[DTO]"                          → gold
├── dto_def_name "GenieCredentialsDto"       → blue
├── "{"                                      → coral
├── dto_property "id"                        → grey
├── dto_property "pass"                      → grey
├── dto_property "provider"                  → grey
├── dto_property "externalId"                → grey
└── "}"                                      → coral
```

Line 12: `    ex:provider.download(UrlDto): DataDto`

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "download"               → teal
├── parameters
│   └── dto_reference "UrlDto"               → blue
└── return_type
    └── dto_reference "DataDto"              → blue
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

Line 44: Type definition with array type

```
[TYP] search: Array<url>
```

```
typ_definition
├── typ_tag "[TYP]"                          → gold
├── typ_name "search"                        → grey
└── typ_type
    └── typ_generic_type
        ├── type_name "Array"                → gold
        ├── "<"                              → coral
        ├── type_name "url"                  → gold
        └── ">"                              → coral
```

Line 58: Type definition with generic type

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
