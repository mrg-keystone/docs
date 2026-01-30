# Recordings App

## Notes

```typescript
interface RecordingAddress {
  id: string;
  provider: string;
}
type Datum = string | number | boolean | Array;
type Metadata = Record<string, Datum> & {
  phoneNumbers: string[];
  id: string;
  provider: string;
};
```

[ ] given a recording address it will retrieve the recording from a remote repository
[ ] given recording address it will save the recording in itself
[ ] given a phone number it will it will retrieve all recording from itself matching that phone number
[ ] given a recording address it will retrieve the recording from itself
[ ] given a recording address it will store metadata on the recording
[ ] given a recording address it will get metadata on a recording

```typescript
interface Comparitor {
    metadataKey: Datum
    operator: '=' | '>' | '<' | '>=' | '<=' | 'includes' |
    bang: boolean
}

interface WebhookConfig {
    url: string,
    authHeader: string,
    filter: Comparitor[][]
}

interface WebhookEvaluationPayload {
    configs: WebhookConfig[],
    metadata: Metadata
}
```

[ ] given a webhookevaluationpayload it should evalutate if it should send the webhook
[ ] given a positive evalutation of a webhook evaluation payload it should send the webhook
