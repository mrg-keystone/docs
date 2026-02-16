[syntax-highlighting]

Editor syntax highlighting for `requirements` files.

## Color Scheme

| Color  | Elements                                               |
| ------ | ------------------------------------------------------ |
| purple | `[REQ]`, concrete tags, DTO references (`*Dto`)        |
| blue   | nouns and verbs (`noun.verb`)                          |
| grey   | params, return types, array brackets, inline DTO props |
| green  | boundary prefixes (`db:`, `ex:`, `os:`, etc.)          |
| red    | faults, inline DTO braces `{}`                         |

Grey is the default for identifiers. Purple overrides grey when the identifier ends in `Dto`.

## Tree-sitter Parser

### Node Types

The parser must produce these node types for highlights.scm to target:

| Node type         | Captures                                 | Color  |
| ----------------- | ---------------------------------------- | ------ |
| `req_tag`         | `[REQ]`                                  | purple |
| `concrete_tag`    | `[GENIE]`, `[FIVE_NINE]`                 | purple |
| `dto_reference`   | identifiers ending in `Dto`              | purple |
| `signature`       | `noun.verb` as a unit                    | —      |
| `identifier`      | noun (before dot)                        | blue   |
| `method_name`     | verb (after dot)                         | blue   |
| `param_name`      | parameter names inside `()`              | grey   |
| `type_name`       | return type identifiers                  | grey   |
| `property_name`   | prop names inside `{}`                   | grey   |
| `boundary_prefix` | `db:`, `ex:`, `os:`, `fs:`, `mq:`, `lg:` | green  |
| `fault_name`      | `not-found`, `timeout`, etc. (lowercase)    | red    |
| `inline_dto`      | region inside `{}`                       | —      |

### Punctuation

- `{` and `}` in inline DTOs → red
- `[` and `]` in array types → grey
- `(`, `)`, `:`, `,` → default

### Treesitter Capture Groups

| Node type         | Capture               | Nvim highlight group |
| ----------------- | --------------------- | -------------------- |
| `req_tag`         | `@keyword`            | Keyword              |
| `concrete_tag`    | `@keyword`            | Keyword              |
| `tag_name`        | `@keyword`            | Keyword              |
| `dto_reference`   | `@type.builtin`       | type.builtin         |
| `identifier`      | `@function`           | Function             |
| `method_name`     | `@function`           | Function             |
| `param_name`      | `@variable.parameter` | variable.parameter   |
| `property_name`   | `@variable.parameter` | variable.parameter   |
| `type_name`       | `@variable.parameter` | variable.parameter   |
| `boundary_prefix` | `@keyword.modifier`   | Keyword              |
| `fault_name`      | `@error`              | Error                |
| inline DTO `{}`   | `@punctuation.special`| Special              |
| array `[]`        | `@variable.parameter` | variable.parameter   |

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

Lines 6-10: Multi-line inline DTO with union return type
```
    ex:provider.search({
      genieAcctId: string,
      genieAcctPass: string
      },
      id: string): UrlDto[] | Array<UrlDto>
```
```
boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → blue
│   └── method_name "search"                 → blue
├── parameters
│   ├── inline_dto
│   │   ├── "{"                              → red
│   │   ├── property_name "genieAcctId"      → grey
│   │   ├── type_name "string"               → grey
│   │   ├── property_name "genieAcctPass"    → grey
│   │   ├── type_name "string"               → grey
│   │   └── "}"                              → red
│   └── param_name "id"                      → grey
│   └── type_name "string"                   → grey
└── return_type
    ├── dto_reference "UrlDto"               → purple
    ├── "[]"                                 → grey
    ├── dto_reference "UrlDto"               → purple
    └── type_name "Array"                    → grey
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
