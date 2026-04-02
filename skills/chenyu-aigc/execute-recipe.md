# Execute a Recipe

Submit a generation task to a specific recipe.

## Request

```bash
curl -s -X POST "$CHENYU_BASE_URL/api/v1/aigc/recipes/{recipe_id}/execute" \
  -H "Authorization: Bearer $CHENYU_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [...],
    "parameters": {...}
  }'
```

**Required headers:**
- `Authorization: Bearer $CHENYU_API_KEY`
- `Idempotency-Key` — unique per request, prevents duplicate submissions (24h TTL)
- `Content-Type: application/json`

## Typed Inputs

Each input has a `type` field and a corresponding payload field:

| type | payload field | key fields |
|------|--------------|------------|
| `text` | `text` | `prompt` (required), `role` (`prompt` / `negative_prompt`) |
| `image` | `image` | `uri` (required), `role` (`first_frame` / `last_frame` / `reference_image`) |
| `video` | `video` | `uri` (required), `role` (`source` / `extend` / `pose`) |
| `audio` | `audio` | `uri` (required), `role` (`background_music` / `speech` / `sound_effect`) |
| `reference` | `reference` | `uri` (required), `kind` (`style` / `element`), `weight` (0.0-1.0) |

**URI formats:** HTTPS URLs, data URIs (`data:image/jpeg;base64,...`), or S3 Asset IDs (26-char ULID).

## Response

```json
{
  "code": 200,
  "data": {
    "task_id": "01HXY...",
    "status": "queued",
    "created_at": "2026-03-07T10:30:00Z"
  }
}
```

After receiving the `task_id`, see [manage-tasks.md](manage-tasks.md) for polling and status management.

## Examples

### Text-to-Video

```bash
curl -s -X POST "$CHENYU_BASE_URL/api/v1/aigc/recipes/{recipe_id}/execute" \
  -H "Authorization: Bearer $CHENYU_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [
      {"type": "text", "text": {"prompt": "A golden retriever playing in snow, cinematic lighting"}}
    ],
    "parameters": {"duration": 5, "ratio": "16:9"}
  }'
```

### Image-to-Video (First Frame)

```bash
curl -s -X POST "$CHENYU_BASE_URL/api/v1/aigc/recipes/{recipe_id}/execute" \
  -H "Authorization: Bearer $CHENYU_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [
      {"type": "text", "text": {"prompt": "Camera slowly zooms in"}},
      {"type": "image", "image": {"uri": "https://example.com/photo.jpg", "role": "first_frame"}}
    ],
    "parameters": {"duration": 5}
  }'
```

### Image-to-Video (First + Last Frame)

```bash
curl -s -X POST "$CHENYU_BASE_URL/api/v1/aigc/recipes/{recipe_id}/execute" \
  -H "Authorization: Bearer $CHENYU_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [
      {"type": "text", "text": {"prompt": "Smooth transition between two scenes"}},
      {"type": "image", "image": {"uri": "https://example.com/start.jpg", "role": "first_frame"}},
      {"type": "image", "image": {"uri": "https://example.com/end.jpg", "role": "last_frame"}}
    ],
    "parameters": {"duration": 5}
  }'
```

### Style Transfer with Reference

```bash
curl -s -X POST "$CHENYU_BASE_URL/api/v1/aigc/recipes/{recipe_id}/execute" \
  -H "Authorization: Bearer $CHENYU_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [
      {"type": "text", "text": {"prompt": "A city street at sunset"}},
      {"type": "reference", "reference": {"uri": "https://example.com/style.jpg", "kind": "style", "weight": 0.8}}
    ],
    "parameters": {"duration": 5}
  }'
```
