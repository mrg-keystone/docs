[syntax-highlighting]

Editor syntax highlighting for `requirements` files.

## Color Scheme

| Hex       | Name   | Elements                                                  |
| --------- | ------ | --------------------------------------------------------- |
| `#E5C07B` | Amber  | Tags: `[REQ]`, `[DTO]`, `[TYP]`, `[PLY]`, `[CSE]`, `[CTR]`, `[RET]` |
| `#61AFEF` | Blue   | DTO references (`*Dto`)                                   |
| `#56B6C2` | Cyan   | Builtin types (`string`, `number`, `Class`, etc.)         |
| `#98C379` | Sage   | Nouns (`noun.verb`, `Noun::verb`)                         |
| `#C678DD` | Violet | Verbs (`noun.verb`, `Noun::verb`)                         |
| `#ABB2BF` | Stone  | Params, properties, TYP names, comments                   |
| `#D19A66` | Peach  | Boundary prefixes (`db:`, `ex:`, `os:`, etc.)             |
| `#E06C75` | Coral  | Faults, brackets (`{}`, `[]`, `<>`)                       |                           |

**Color groupings:**
- **Type system** (cool): Blue for DTO refs, Cyan for builtins
- **Code flow** (complementary): Sage for nouns, Violet for verbs
- **Structure** (warm): Amber for tags, Peach for boundaries, Coral for faults

## Tree-sitter Parser

### Node Types

The parser must produce these node types for highlights.scm to target:

| Node type         | Captures                                    | Color  |
| ----------------- | ------------------------------------------- | ------ |
| `req_tag`         | `[REQ]`                                     | amber  |
| `ply_tag`         | `[PLY]`                                     | amber  |
| `cse_tag`         | `[CSE]`                                     | amber  |
| `ctr_tag`         | `[CTR]`                                     | amber  |
| `ret_tag`         | `[RET]`                                     | amber  |
| `dto_tag`         | `[DTO]`                                     | amber  |
| `typ_tag`         | `[TYP]`                                     | amber  |
| `dto_reference`   | identifiers ending in `Dto`                 | blue   |
| `signature`       | `noun.verb` or `Noun::verb` as a unit       | —      |
| `identifier`      | noun (before `.` or `::`)                   | sage   |
| `method_name`     | verb (after `.` or `::`)                    | violet |
| `param_name`      | parameter names inside `()`                 | stone  |
| `type_name`       | return type identifiers                     | stone  |
| `property_name`   | prop names inside `{}`                      | stone  |
| `boundary_prefix` | `db:`, `ex:`, `os:`, `fs:`, `mq:`, `lg:`    | peach  |
| `fault_line`      | container for one or more faults            | —      |
| `fault_name`      | `not-found`, `timed-out`, etc.              | coral  |
| `inline_dto`      | region inside `{}`                          | —      |
| `dto_def_name`    | DTO name after `[DTO]` tag                  | blue   |
| `dto_prop`        | property name after `:` in DTO definition   | stone  |
| `dto_array_prop`  | array property base, e.g. `url` in `url(s)` | stone  |
| `dto_array_suffix`| plural suffix `(s)`, `(es)`, `(ren)`        | stone  |
| `dto_desc`        | description line (4-space indent after DTO) | stone  |
| `typ_name`        | name before `:`                             | stone  |
| `typ_type`        | type after `:` (builtin primitives)         | cyan   |
| `typ_generic_type`| generic like `Array<url>`, `Record<K, V>`   | cyan   |
| `typ_tuple_type`  | tuple like `[id, name]`                     | cyan   |
| `ply_step`        | `[PLY] noun.verb(): type` polymorphic step  | —      |
| `cse_step`        | `[CSE] name` case inside poly block         | —      |
| `ctr_step`        | `[CTR] class` constructor step              | —      |
| `ret_step`        | `[RET] value` return step                   | —      |
| `comment`         | `// text` inline comments                   | stone  |

### Punctuation

- `{` `}`, `[` `]`, `<` `>`, `(`, `)`, `:`, `::`, `,`, `|` → coral

### Treesitter Capture Groups

| Node type         | Capture                | Color (hex)  |
| ----------------- | ---------------------- | ------------ |
| `req_tag`         | `@keyword`             | `#E5C07B`    |
| `ply_tag`         | `@keyword`             | `#E5C07B`    |
| `cse_tag`         | `@keyword`             | `#E5C07B`    |
| `ctr_tag`         | `@keyword`             | `#E5C07B`    |
| `ret_tag`         | `@keyword`             | `#E5C07B`    |
| `dto_tag`         | `@keyword`             | `#E5C07B`    |
| `typ_tag`         | `@keyword`             | `#E5C07B`    |
| `dto_reference`   | `@type.builtin`        | `#61AFEF`    |
| `identifier`      | `@lsp.type.class`      | `#98C379`    |
| `method_name`     | `@function.method`     | `#C678DD`    |
| `param_name`      | `@variable.parameter`  | `#ABB2BF`    |
| `property_name`   | `@variable.parameter`  | `#ABB2BF`    |
| `type_name`       | `@variable.parameter`  | `#ABB2BF`    |
| `boundary_prefix` | `@attribute`           | `#D19A66`    |
| `fault_name`      | `@error`               | `#E06C75`    |
| `dto_def_name`    | `@type.builtin`        | `#61AFEF`    |
| `dto_prop`        | `@variable.parameter`  | `#ABB2BF`    |
| `dto_array_prop`  | `@variable.parameter`  | `#ABB2BF`    |
| `dto_array_suffix`| `@variable.parameter`  | `#ABB2BF`    |
| `dto_desc`        | `@comment`             | `#ABB2BF`    |
| `typ_name`        | `@variable.parameter`  | `#ABB2BF`    |
| `typ_type`        | `@type`                | `#56B6C2`    |
| `typ_generic_type`| `@type`                | `#56B6C2`    |
| `typ_tuple_type`  | `@type`                | `#56B6C2`    |
| `comment`         | `@comment`             | `#ABB2BF`    |
| `{}` `[]` `<>`    | `@punctuation.special` | `#E06C75`    |

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

Line 17: `    [CTR] metadata` (constructor shorthand)

```
ctr_step
├── ctr_tag "[CTR]"                          → gold
└── identifier "metadata"                    → teal
```

- No parentheses or return type
- Implicitly returns the class type
- Adds class name to scope

Line 3: `      not-valid-provider`

```
fault_line
└── fault_name "not-valid-provider"          → coral
```

Line 9: `          not-found timed-out invalid-id` (multi-fault line)

```
fault_line
├── fault_name "not-found"                   → coral
├── fault_name "timed-out"                   → coral
└── fault_name "invalid-id"                  → coral
```

Lines 6-16: Polymorphic block

```
    [PLY] provider.getRecording(externalId): data  ← 4 spaces
        [CSE] genie                                ← 8 spaces
        ex:provider.search(externalId): SearchDto  ← 8 spaces
          not-found timed-out invalid-id           ← 10 spaces
        ex:provider.download(url): data            ← 8 spaces
          not-found timed-out                      ← 10 spaces
        [CSE] fiveNine                             ← 8 spaces
        ex:provider.search(externalId): SearchDto  ← 8 spaces
          not-found timed-out invalid-id           ← 10 spaces
        ex:provider.download(url): data            ← 8 spaces
          not-found timed-out                      ← 10 spaces
    [CTR] metadata                                 ← 4 spaces (ends poly)
```

```
ply_step
├── ply_tag "[PLY]"                          → gold
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "getRecording"           → teal
├── parameters
│   └── param_name "externalId"              → grey
└── return_type
    └── type_name "data"                     → grey

cse_step
├── cse_tag "[CSE]"                          → gold
└── identifier "genie"                       → teal

boundary_line
├── boundary_prefix "ex:"                    → green
├── signature
│   ├── identifier "provider"                → teal
│   └── method_name "search"                 → teal
...
```

Line 7: `        [CSE] genie`

```
cse_step
├── cse_tag "[CSE]"                          → gold
└── identifier "genie"                       → teal
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

Lines 79-80: DTO definition

```
[DTO] SearchDto: url(s)
    a list of URLs returned by provider search
```

```
dto_definition
├── dto_tag "[DTO]"                          → gold
├── dto_def_name "SearchDto"                 → blue
├── dto_array_prop "url"                     → grey
├── dto_array_suffix "(s)"                   → coral
└── dto_desc "a list of URLs..."             → grey
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
