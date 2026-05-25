# research.md

## Decisions

- Decision: Language and Framework
  - Chosen: Java 21 + Spring Boot 3.5
  - Rationale: Repository constitution mandates Java 21 and Spring Boot; aligns with existing project stack and minimizes friction.
  - Alternatives considered: Lightweight script (curl + shell) — rejected to keep monitoring integrated and testable within project.

- Decision: Project Type
  - Chosen: Web service / monitoring client within repo
  - Rationale: Feature integrates with existing Spring Boot codebase and CI practices.

- Decision: Storage
  - Chosen: No persistent storage required for ping results (ephemeral logs + monitoring system)
  - Rationale: Spec requires recording failures for investigation within 24 hours; this can be satisfied by structured logs and existing observability stack (Actuator + Micrometer + log aggregation).

- Decision: Authentication
  - Chosen: Unauthenticated health/ping endpoint (confirmed)
  - Rationale: Spec and stakeholder confirmation prefer unauthenticated ping to minimize false negatives from auth failures.

- Decision: Testing
  - Chosen: JUnit 5 + Testcontainers for integration where applicable; unit tests for logic
  - Rationale: Matches repository conventions and constitution.

## Research Notes

- Performance target: Ping response judged healthy if 2xx within 5s (per spec). No further low-latency SLO required for this feature.
- Observability: Use structured logs (latency, status code, timestamp) and expose metrics via Micrometer if longer-term tracking required.

## Artifacts to produce next

- data-model.md
- quickstart.md
- contracts/ (empty unless external contracts needed)
