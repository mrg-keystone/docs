[syntax-highlighting]

Editor syntax highlighting for `requirements` files.

## Color Scheme

| Color | Elements                                                   |
| ----- | ---------------------------------------------------------- |
| gold  | `[REQ]`, concrete tags, primitive types (`string`, `void`) |
| blue  | DTO references (`*Dto`)                                    |
| teal  | nouns and verbs (`noun.verb`)                              |
| grey  | params, property names                                     |
| green | boundary prefixes (`db:`, `ex:`, `os:`, etc.)              |
| coral | faults, brackets (`{}`, `[]`, `<>`)                        |

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
| `fault_name`      | `not-found`, `timed-out`, etc. (kebab-case) | coral  |
| `inline_dto`      | region inside `{}`                          | —      |
| `dto_tag`         | `[DTO]`                                     | gold   |
| `dto_def_name`    | DTO name after `[DTO]` tag                  | blue   |

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
| `fault_name`      | `@error`               | Error                |
| `dto_tag`         | `@keyword`             | Keyword              |
| `dto_def_name`    | `@type.builtin`        | type.builtin         |
| `{}` `[]` `<>`    | `@punctuation.special` | Special              |

### Expected AST

Based on `./requirements`:

Line 1: `[REQ] recording.set(getRecordingDto): RecordingSetResponseDto`

```
req_line
├── req_tag "[REQ]"                          → purple
├── signature
│   ├── identifier "recording"               → blue
│   └── method_name "set"                    → blue
├── parameters
│   └── dto_reference "getRecordingDto"      → purple
└── return_type
    └── dto_reference "RecordingSetResponseDto" → purple
```

Line 2: `    id.build(providerName, externalRecordingId): internalRecordingId`

```
step_line
├── signature
│   ├── identifier "id"                      → blue
│   └── method_name "build"                  → blue
├── parameters
│   ├── param_name "providerName"            → grey
│   └── param_name "externalRecordingId"     → grey
└── return_type
    └── type_name "internalRecordingId"      → grey
```

Line 3: `      not-valid-provider`

```
fault_name "not-valid-provider"              → red
```

Line 5: `    [GENIE]`

```
concrete_tag "[GENIE]"                       → purple
```

Line 6: Boundary step with DTO reference and union return type

```
    ex:provider.search(GenieCredentialsDto, id: string): UrlDto[] | Array<UrlDto>
```

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "search"                 → teal
├── parameters
│   ├── dto_reference "GenieCredentialsDto"  → blue
│   └── typed_param
│       ├── param_name "id"                  → grey
│       └── type_name "string"               → gold
└── return_type
    ├── dto_reference "UrlDto"               → blue
    ├── "[]"                                 → coral
    ├── type_name "Array"                    → gold
    ├── "<"                                  → coral
    ├── dto_reference "UrlDto"               → blue
    └── ">"                                  → coral
```

Lines 48-52: DTO definition block

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

Line 13: `    ex:provider.download(UrlDto): recordingBinaryData`

```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → blue
│   └── method_name "download"               → blue
├── parameters
│   └── dto_reference "UrlDto"               → purple
└── return_type
    └── type_name "recordingBinaryData"      → grey
```

Line 16: `    db:metadata.set(internalRecordingId, metadata): void`

```
boundary_line
├── boundary_prefix "db:"                    → green
├── signature
│   ├── identifier "metadata"                → blue
│   └── method_name "set"                    → blue
├── parameters
│   ├── param_name "internalRecordingId"     → grey
│   └── param_name "metadata"                → grey
└── return_type
    └── type_name "void"                     → grey
```
