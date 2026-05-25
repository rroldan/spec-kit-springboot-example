# quickstart.md

## Running a manual ping

1. From the repository root, run a curl check manually:

   curl -i -m 5 https://api.example.com/

2. Validate: HTTP status 200-299 and response within 5 seconds.

## Running automated checks (developer guidance)

- Implement a scheduled Spring component or a simple CLI task that performs the ping and logs structured JSON with fields: status, status_code, latency_ms, timestamp.
- For local testing, run the ping method in a unit or integration test and assert expected behavior using a mocked endpoint or Testcontainers-hosted service.

