# data-model.md

## Entities

- API Endpoint
  - Fields:
    - base_url: string (required) — e.g., https://api.example.com
    - name: string (optional)

- Ping Result
  - Fields:
    - status: string ("success" | "failure")
    - status_code: integer | null
    - latency_ms: integer (round-trip in milliseconds)
    - timestamp: string (ISO 8601)
    - body_snippet: string (optional, truncated)

## Storage & Retention

- No relational storage required by this feature. Ping results are emitted to logs and metrics.
- If persistent retention is later required, store Ping Result in a lightweight time-series or events table with 30-day retention.

## Validation Rules

- status must be "success" if status_code in [200..299] and latency_ms <= 5000
- latency_ms must be non-negative integer

