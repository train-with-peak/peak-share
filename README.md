# peak-share

Static redirect page for sharing Peak workout templates via HTTPS links.

## How it works

When a Peak user shares a workout, the app generates an HTTPS link:

```
https://train-with-peak.github.io/peak-share/?d=<base64-encoded-workout>
```

The page decodes the Base64 payload, displays a workout preview, and offers an "Open in Peak" button that triggers a `peak://import-workout` deep link.

## URL format

| Parameter | Description |
|-----------|-------------|
| `d` | URL-safe Base64-encoded JSON workout template (max 10,000 chars) |

### Payload schema

```json
{
  "version": 1,
  "name": "Push Day",
  "exercises": [
    { "name": "Bench Press", "category": "push", "muscleGroup": "Chest", "equipment": "Barbell", "sets": 4 }
  ]
}
```

### Limits

- Max payload size: 10,000 characters
- Max exercises per workout: 20

## Security

- **Content Security Policy** restricts all resource loading
- No `eval()`, no `innerHTML` with user data — only `textContent` and `createTextNode()`
- JSON schema validation before rendering
- Exercise array size guard (max 20)
- Individual exercise field type validation
- Payload size guard (10,000 char limit)
- No tracking, cookies, localStorage, or external requests
- All data lives in the URL — nothing is stored server-side
