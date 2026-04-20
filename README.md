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
| `d` | URL-safe Base64-encoded JSON workout template (max 14,000 chars) |

### Payload schema (v2)

```json
{
  "version": 2,
  "name": "Push Day",
  "exercises": [
    {
      "id": "bench_press",
      "name": "Bench Press",
      "category": "push",
      "muscleGroup": "Chest",
      "equipment": "Barbell",
      "sets": 4,
      "configurationSelection": {
        "bench_angle": "Incline",
        "grip": "Medium"
      }
    }
  ]
}
```

Both `version: 1` and `version: 2` payloads are accepted. v2 adds the optional `id` (catalog key) and `configurationSelection` map (variant/grip/stance/position/bench_angle/handle), which surface as the "· variant" chip in the preview.

### Limits

- Max payload size: 14,000 characters
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

## Branding Assets

- `logo.png`: in-page logo shown above the shared workout details.
  - Keep aspect ratio intact (no forced square resize in CSS/HTML).
  - Current source is the app logo exported from Peak assets.
- `og-image-1200x630-v3.png`: current social/link preview image used by crawlers.
  - Required size: **1200 x 630** (landscape).
  - Keep the logo centered with aspect-fit on a dark background.
  - Use a new filename suffix (`-v4`, `-v5`, ...) when updating previews.

### Social preview cache busting

- Do not rely only on query params for social cache refresh.
- When preview art changes, publish a new file name (for example `og-image-1200x630-v4.png`) and update OG/Twitter tags in `index.html`.
- Some crawlers (WhatsApp/Facebook) can cache previews for a while; filename versioning is the most reliable invalidation path.
