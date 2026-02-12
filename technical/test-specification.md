[test-specification]

Defines the notation used in `requirements` files.

## Requirement

- Format: `[REQ] Noun.Verb(inputDto): OutputDto`
- Represents the happy-path outcome (e2e test)
- Input args use `Dto` suffix (external input needing validation)
- Return type is the output DTO (PascalCase)
- Faults live under steps, not on the REQ line
- One requirement per feature entry point

```
[REQ] recording.set(getRecordingDto): RecordingSetResponseDto
[REQ] recording.get(getRecordingDto): RecordingDto
[REQ] recording.add-metadata(addMetadataDto): addMetadataResponseDto
```

## Step

- Indented 4 spaces under parent requirement
- Format: `Noun.Verb(args): return-type`
- No blank lines between steps within a requirement
- Double blank line between requirements

### Boundary tags

2-char colon prefix on steps that cross a system boundary. Business logic steps stay untagged.

| Tag  | Boundary                       |
| ---- | ------------------------------ |
| `db` | database / persistence         |
| `fs` | file system (local)            |
| `mq` | message queue                  |
| `ex` | external service / provider    |
| `os` | object storage (S3, GCS, etc.) |
| `lg` | logs                           |

Example: `db:metadata.set(internalRecordingId, metadata): void`

### Polymorphic steps

When a step Noun names an interface rather than a concrete class, the step is polymorphic. The interface line declares the signature at step indent (4 spaces). Concrete implementations are listed below it as `[UPPER_CASE]` at step indent (4 spaces). Each concrete carries its own sub-steps (6 spaces) and faults (8 spaces).

```
    provider.get(externalRecordingId): recordingData
    [GENIE]
      ex:search(...): url
        not-found
        timeout
      ex:download(url): recordingData
        not-found
        timeout
    [FIVE_NINE]
      ex:search(...): url
        not-found
```

- The interface line has no faults of its own
- `[UPPER_CASE]` at 4-space indent declares a concrete implementation
- Sub-steps under each concrete are indented 4 spaces
- Faults on sub-steps are indented 6 spaces
- Each concrete can have different sub-steps; they only share the interface return type

### Dto suffix

Args ending in `Dto` are external inputs requiring validation. Internal args (passed between steps) have no suffix.

## Fault

- Indented 6 spaces (2 deeper than parent step)
- One fault per line
- Fault names describe _why_ something didn't succeed (not just "failed")
- Each fault implies a test case
- Steps with no faults cannot fail

## Traced Example

```
[REQ] recording.set(getRecordingDto): RecordingSetResponseDto
    id.build(provider, externalRecordingId): internalRecordingId
      not-valid-provider
    provider.get(externalRecordingId): recordingData
    [GENIE]
      ex:search(...): url
        not-found
        timeout
      ex:download(url): recordingData
        not-found
        timeout
    [FIVE_NINE]
      ex:search(...): url
        not-found
        timeout
      ex:download(url): recordingData
        not-found
        timeout
    [READYMODE]
      ex:search(...): url
        not-found
        timeout
      ex:download(url): recordingData
        not-found
        timeout
    db:metadata.set(internalRecordingId, metadata): void
      timeout
      network-error
    os:storage.save(internalRecordingId, recordingData): void
      timeout
      network-error
```

`recording.set` has 4 steps. The `provider.get` step is polymorphic with 3 concretes, each having 2 sub-steps and 4 faults. The 2 remaining steps have 2 faults each. Total: 1 + (3 x 4) + 2 + 2 = 17 faults, yielding 4 happy-path + 17 fault-path = 21 test cases.

## File Conventions

- File named `requirements` (no extension)
- Indentation: 4 spaces for steps, 6 spaces for faults
- No blank lines between steps; double blank line between requirements
