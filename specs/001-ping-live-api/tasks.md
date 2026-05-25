# Tasks for: Ping Live API

Feature: Ping Live API (spec.md)

Notes:
- Each task is independently actionable and formatted as a checklist line with a Task ID.
- Implementation language: Java 21 / Spring Boot 3.5

## Phase 1 — Setup

- [ ] T001 Create configuration properties class PingProperties in src/main/java/com/example/app/config/PingProperties.java (adds fields for endpoints list and default interval)
- [ ] T002 Add default ping configuration to src/main/resources/application.yml under `monitoring.ping` (example endpoint placeholder and default interval)

## Phase 2 — Foundational

- [ ] T003 Create DTO PingResult in src/main/java/com/example/app/dto/PingResult.java (fields: status, statusCode, latencyMs, timestamp, bodySnippet)
- [ ] T004 Implement PingClient in src/main/java/com/example/app/service/PingClient.java (HTTP GET with 5s timeout; method: PingResult ping(String baseUrl))
- [ ] T005 Implement PingService in src/main/java/com/example/app/service/PingService.java (orchestrates calls to PingClient for configured endpoints; logs structured JSON with status, statusCode, latencyMs, timestamp; expose Micrometer metrics if available)

## Phase 3 — User Stories

### User Story 1 - Ping Live API (Priority: P1)

- [ ] T006 [US1] Create PingController in src/main/java/com/example/app/controller/PingController.java (GET /internal/ping?url=<url>) to trigger a single ping for manual testing; returns PingResult as JSON
- [ ] T007 [US1] Create PingScheduler in src/main/java/com/example/app/service/PingScheduler.java (scheduled at configurable interval from PingProperties; invokes PingService for configured endpoints)
- [ ] T008 [P] [US1] Add unit tests for PingClient in src/test/unit/PingClientTest.java (mock HTTP responses; assert timeout handling and 2xx mapping to success)
- [ ] T009 [US1] Add integration test PingServiceIT in src/test/integration/PingServiceIT.java using a local mock HTTP server (e.g., MockWebServer/WireMock) to assert success on 2xx within 5s and failure on 5xx/timeout

## Final Phase — Polish & Cross-Cutting

- [ ] T010 Add Micrometer metrics (counter for failures, timer for latency) in src/main/java/com/example/app/service/PingService.java and update quickstart.md at specs/001-ping-live-api/quickstart.md with usage notes

## Dependencies & Notes

- Tests may use Mockito, JUnit 5, and a lightweight mock HTTP server (WireMock or MockWebServer).
- Do not persist PingResult to DB; emit structured logs and metrics per spec.

## Summary
- Total tasks: 10
- Tasks for User Story 1: 5 (T006-T009, plus foundational tasks used by the story)
- Parallel opportunities: T008 (unit tests) and T010 (metrics/documentation) marked [P]
- Suggested MVP: Complete T001-T007 and T008 (manual trigger + scheduler + basic logging) to validate acceptance scenarios

