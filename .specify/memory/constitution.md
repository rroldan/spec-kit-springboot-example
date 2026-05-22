# Spring Boot Example Constitution
 
> **Version:** 1.0.0 · **Ratified:** 2026-05-21 · **Last Amended:** 2026-05-22 · **Owner:** rroldan
 
---
 
## Core Principles
 
### I. Clear Intent
All work in this repository MUST start from an explicit feature specification. Every feature MUST name the user value, scope, and acceptance criteria before design or implementation begins.
 
### II. Minimal Viable Design
Design decisions MUST be constrained to the smallest safe shape needed to deliver value. Implementation plans MUST avoid unnecessary architectural complexity, feature bloat, and unvalidated abstractions.
 
### III. Evidence-Driven Work
Every change MUST be justified by a documented need, a user scenario, or a measurable outcome. Proposed work MUST include acceptance scenarios, success criteria, and a plan for validation before development begins.
 
### IV. Continuous Review
All specifications, plans, and tasks MUST be reviewed against this constitution. Changes to requirements or design MUST be recorded and revalidated rather than hidden in implementation.
 
### V. Git-First Discipline
This repository MUST use branch-based work with a default branch of `main`. Commits MUST be atomic, documented, and aligned with the Spec Kit workflow. Uncommitted changes SHOULD be resolved before moving between planning phases.
 
---
 
## Project Scope
 
This constitution applies to the Spec Kit Spring Boot example repository and governs how features are specified, planned, and tracked. It sets expectations for repository structure, documentation discipline, and traceability; it does not prescribe technology choices beyond supporting the Spec Kit workflow.
 
---
 
## Development Workflow
 
Specs, plans, and tasks MUST be created using the `.specify/templates/` workflow. Feature work MUST follow the sequence:
 
1. Create a feature branch.
2. Write or update `spec.md`.
3. Generate `plan.md`.
4. Generate `tasks.md`.
5. Implement according to the approved plan and task sequence.
Reviews MUST verify that every feature proposal includes:
 
- a clear problem statement,
- measurable success criteria,
- a Constitution Check consistent with the Project Scope section,
- and a committed project structure that matches the current repo layout.
---
 
## Governance
 
This constitution supersedes informal practices and is the authoritative source for repository workflow. Amendments MUST be documented in this file, reviewed, and accompanied by a version bump and migration notes. Compliance reviews MUST occur before major feature milestones and before `speckit.tasks` output is finalized.
 
- Amendments require a documented proposal, one review pass, and a new `Last Amended` date.
- Version changes MUST follow semantic versioning: MAJOR for governance shifts, MINOR for new principles or workflow sections, PATCH for wording and clarification updates.
- Every feature branch MUST reference this constitution in its planning artifacts.
- Use `.specify/templates/` files as the runtime guidance source for specs, plans, and task generation.
---
 
## 1. Project Information
 
| Field               | Value                                                        |
|---------------------|--------------------------------------------------------------|
| Name                | `spec-kit-springboot-example`                                |
| Java Version        | 21                                                           |
| Spring Boot Version | 3.5                                                          |
| Build Tool          | Maven                                                        |
| Repository          | https://github.com/rroldan/spec-kit-springboot-example       |
| Owner               | rroldan                                                      |
 
---
 
## 2. Project Structure
 
```
src/
├── main/
│   ├── java/com/example/app/
│   │   ├── config/          # Spring configuration (Security, CORS, Beans)
│   │   ├── controller/      # REST controllers
│   │   ├── service/         # Business logic
│   │   ├── repository/      # Data access layer (JPA / Spring Data)
│   │   ├── model/           # JPA entities
│   │   ├── dto/             # Data Transfer Objects
│   │   ├── exception/       # Custom exceptions and handlers
│   │   └── Application.java # Main class
│   └── resources/
│       ├── application.yml
│       ├── application-dev.yml
│       ├── application-pre.yml
│       └── application-prod.yml
└── test/
    ├── unit/
    ├── integration/
    └── e2e/
.specify/
└── templates/
    ├── spec.md
    ├── plan.md
    └── tasks.md
```
 
---
 
## 3. Naming Conventions
 
| Element        | Convention       | Example                            |
|----------------|------------------|------------------------------------|
| Classes        | PascalCase       | `UserService`, `OrderController`   |
| Methods        | camelCase        | `findUserById()`, `createOrder()`  |
| Variables      | camelCase        | `userId`, `orderList`              |
| Constants      | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT`                  |
| Packages       | lowercase        | `com.example.app.service`          |
| DB Tables      | snake_case       | `user_orders`, `product_detail`    |
| REST Endpoints | kebab-case plural| `/api/v1/user-profiles`            |
 
---
 
## 4. Standard Dependencies
 
```xml
<!-- pom.xml — core dependencies -->
<dependencies>
  <!-- Web -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
 
  <!-- Validation -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
  </dependency>
 
  <!-- Persistence -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
 
  <!-- Security -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
 
  <!-- Observability -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
 
  <!-- API Documentation -->
  <dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.x.x</version>
  </dependency>
 
  <!-- DB Migrations -->
  <dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
  </dependency>
 
  <!-- Utilities -->
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
  </dependency>
</dependencies>
```
 
---
 
## 5. Environment Configuration
 
Three active profiles are defined: `dev`, `pre`, and `prod`.
 
```yaml
# application.yml (base)
spring:
  application:
    name: spec-kit-springboot-example
  profiles:
    active: dev
 
server:
  port: 8080
 
logging:
  level:
    root: INFO
    com.example.app: DEBUG
```
 
```yaml
# application-pre.yml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USER}
    password: ${DB_PASSWORD}
  jpa:
    show-sql: false
 
logging:
  level:
    root: INFO
```
 
```yaml
# application-prod.yml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USER}
    password: ${DB_PASSWORD}
  jpa:
    show-sql: false
 
logging:
  level:
    root: WARN
```
 
### Required Environment Variables
 
| Variable      | Description                | Example                          |
|---------------|----------------------------|----------------------------------|
| `DB_URL`      | Database connection URL    | `jdbc:postgresql://host:5432/db` |
| `DB_USER`     | Database username          | `app_user`                       |
| `DB_PASSWORD` | Database password          | `*****`                          |
| `JWT_SECRET`  | Secret key for JWT signing | `your-256-bit-secret`            |
| `APP_PORT`    | Application port           | `8080`                           |
 
---
 
## 6. REST API
 
### Versioning
 
All public routes must be versioned:
 
```
/api/v1/resources
```
 
### Standard Response Format
 
**Success:**
```json
{
  "status": "success",
  "data": { },
  "timestamp": "2026-05-22T10:00:00Z"
}
```
 
**Error:**
```json
{
  "status": "error",
  "code": "RESOURCE_NOT_FOUND",
  "message": "The requested resource does not exist.",
  "timestamp": "2026-05-22T10:00:00Z"
}
```
 
### HTTP Status Codes
 
| Code | Usage                          |
|------|--------------------------------|
| 200  | Successful operation           |
| 201  | Resource created               |
| 204  | No content (successful DELETE) |
| 400  | Invalid input data             |
| 401  | Unauthenticated                |
| 403  | Unauthorized                   |
| 404  | Resource not found             |
| 409  | Conflict (e.g. duplicate)      |
| 500  | Internal server error          |
 
### Global Error Handler
 
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
 
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("RESOURCE_NOT_FOUND", ex.getMessage()));
    }
 
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        // map validation errors
    }
}
```
 
---
 
## 7. API Documentation
 
All REST endpoints MUST be documented using **SpringDoc OpenAPI 3** (Swagger UI). Documentation MUST include descriptions, request/response examples, and error schemas for every endpoint.
 
### Dependency
 
```xml
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.5.0</version>
</dependency>
```
 
### Global OpenAPI Configuration
 
```java
@Configuration
public class OpenApiConfig {
 
    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Spec Kit Spring Boot Example API")
                .description("REST API for the Spec Kit Spring Boot example project.")
                .version("1.0.0")
                .contact(new Contact()
                    .name("rroldan")
                    .url("https://github.com/rroldan/spec-kit-springboot-example")))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"))
            .components(new Components()
                .addSecuritySchemes("bearerAuth", new SecurityScheme()
                    .type(SecurityScheme.Type.HTTP)
                    .scheme("bearer")
                    .bearerFormat("JWT")));
    }
}
```
 
### application.yml — Swagger UI settings
 
```yaml
springdoc:
  api-docs:
    path: /api-docs
  swagger-ui:
    path: /swagger-ui.html
    operationsSorter: method
    tagsSorter: alpha
    tryItOutEnabled: true
  show-actuator: false
```
 
Swagger UI is available at `/swagger-ui.html`. The raw OpenAPI spec is at `/api-docs`.
 
### Annotating a Controller
 
Every controller MUST carry a `@Tag` annotation. Every endpoint MUST carry `@Operation` with a summary, description, and at least one `@ApiResponse`.
 
```java
@RestController
@RequestMapping("/api/v1/users")
@Tag(name = "Users", description = "Operations for managing user accounts")
public class UserController {
 
    @Operation(
        summary = "Get user by ID",
        description = "Returns a single user identified by their UUID."
    )
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "User found",
            content = @Content(mediaType = "application/json",
                schema = @Schema(implementation = UserResponse.class),
                examples = @ExampleObject(name = "success", value = """
                    {
                      "status": "success",
                      "data": {
                        "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
                        "email": "jane.doe@example.com",
                        "createdAt": "2026-01-15T09:00:00Z"
                      },
                      "timestamp": "2026-05-22T10:00:00Z"
                    }
                    """))),
        @ApiResponse(responseCode = "404", description = "User not found",
            content = @Content(mediaType = "application/json",
                examples = @ExampleObject(name = "not_found", value = """
                    {
                      "status": "error",
                      "code": "RESOURCE_NOT_FOUND",
                      "message": "User with id 3fa85f64 does not exist.",
                      "timestamp": "2026-05-22T10:00:00Z"
                    }
                    """))),
        @ApiResponse(responseCode = "401", description = "Unauthenticated")
    })
    @GetMapping("/{id}")
    public ResponseEntity<ApiResponse<UserResponse>> getById(
            @Parameter(description = "User UUID", required = true)
            @PathVariable UUID id) {
        return ResponseEntity.ok(ApiResponse.success(userService.findById(id)));
    }
 
    @Operation(
        summary = "Create a new user",
        description = "Registers a new user account. Returns the created user."
    )
    @ApiResponses({
        @ApiResponse(responseCode = "201", description = "User created",
            content = @Content(mediaType = "application/json",
                examples = @ExampleObject(name = "created", value = """
                    {
                      "status": "success",
                      "data": {
                        "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
                        "email": "jane.doe@example.com",
                        "createdAt": "2026-05-22T10:00:00Z"
                      },
                      "timestamp": "2026-05-22T10:00:00Z"
                    }
                    """))),
        @ApiResponse(responseCode = "400", description = "Invalid request body",
            content = @Content(mediaType = "application/json",
                examples = @ExampleObject(name = "validation_error", value = """
                    {
                      "status": "error",
                      "code": "VALIDATION_FAILED",
                      "message": "email: must be a valid email address",
                      "timestamp": "2026-05-22T10:00:00Z"
                    }
                    """))),
        @ApiResponse(responseCode = "409", description = "Email already registered")
    })
    @PostMapping
    public ResponseEntity<ApiResponse<UserResponse>> create(
            @io.swagger.v3.oas.annotations.parameters.RequestBody(
                description = "User registration payload",
                required = true,
                content = @Content(examples = @ExampleObject(name = "request", value = """
                    {
                      "email": "jane.doe@example.com",
                      "password": "Str0ng!Pass"
                    }
                    """)))
            @RequestBody @Valid CreateUserRequest request) {
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(ApiResponse.success(userService.create(request)));
    }
}
```
 
### Annotating a DTO
 
Request and response DTOs MUST use `@Schema` to document every field with a description and example value.
 
```java
@Schema(description = "Payload for creating a new user account")
public record CreateUserRequest(
 
    @Schema(description = "User email address", example = "jane.doe@example.com")
    @NotBlank @Email
    String email,
 
    @Schema(description = "Password — minimum 8 characters, at least one digit and one special character",
            example = "Str0ng!Pass")
    @NotBlank @Size(min = 8)
    String password
) {}
 
@Schema(description = "User data returned by the API")
public record UserResponse(
 
    @Schema(description = "Unique user identifier", example = "3fa85f64-5717-4562-b3fc-2c963f66afa6")
    UUID id,
 
    @Schema(description = "User email address", example = "jane.doe@example.com")
    String email,
 
    @Schema(description = "Account creation timestamp", example = "2026-01-15T09:00:00Z")
    LocalDateTime createdAt
) {}
```
 
### Rules
 
- Swagger UI MUST be **disabled in `prod`** — enabled only in `dev` and `pre`.
- Every new endpoint added MUST include at minimum: `@Operation`, one success `@ApiResponse`, and one error `@ApiResponse`.
- Examples MUST use realistic, non-trivial values (not `string`, `0`, or `null`).
```yaml
# application-prod.yml — disable Swagger UI in production
springdoc:
  swagger-ui:
    enabled: false
  api-docs:
    enabled: false
```
 
---
 
## 8. Security
 
### Authentication Strategy
 
- Authentication based on **JWT (Bearer Token)**
- Token expiry: **15 minutes** (access) and **7 days** (refresh)
- Passwords stored with **BCrypt** (strength = 12)
### Public Endpoints
 
```
POST /api/v1/auth/login
POST /api/v1/auth/register
POST /api/v1/auth/refresh
GET  /actuator/health
```
 
### Roles and Permissions
 
| Role         | Description                     |
|--------------|---------------------------------|
| `ROLE_USER`  | Standard user — basic access    |
| `ROLE_ADMIN` | Administrator — full access     |
 
---
 
## 9. Persistence
 
### Entity Conventions
 
```java
@Entity
@Table(name = "users")
@Getter @Setter
@NoArgsConstructor
public class User {
 
    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;
 
    @Column(nullable = false, unique = true)
    private String email;
 
    @Column(name = "created_at", updatable = false)
    private LocalDateTime createdAt;
 
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
}
```
 
### Flyway Migrations
 
Migration scripts go in `src/main/resources/db/migration/` following this naming pattern:
 
```
V1__create_users_table.sql
V2__add_orders_table.sql
V3__add_index_on_email.sql
```
 
### Pagination
 
All list endpoints must support pagination:
 
```
GET /api/v1/users?page=0&size=20&sort=createdAt,desc
```
 
---
 
## 10. Testing
 
### Minimum Coverage Requirements
 
| Layer      | Minimum Coverage |
|------------|-----------------|
| Service    | 80%             |
| Controller | 70%             |
| Repository | 60%             |
 
### Test Structure
 
```java
// Unit test — service layer
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock UserRepository userRepository;
    @InjectMocks UserService userService;
 
    @Test
    void shouldReturnUserWhenExists() { ... }
}
 
// Integration test — web layer
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired MockMvc mockMvc;
    @MockBean UserService userService;
 
    @Test
    void shouldReturn200WhenUserFound() throws Exception { ... }
}
 
// Integration test — with real database
@SpringBootTest
@Testcontainers
class UserRepositoryIntegrationTest {
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");
    ...
}
```
 
---
 
## 11. Logging and Observability
 
### Log Levels by Environment
 
| Environment | Root  | App package |
|-------------|-------|-------------|
| dev         | INFO  | DEBUG       |
| pre         | INFO  | INFO        |
| prod        | WARN  | INFO        |
 
### Log Format (JSON in production)
 
```yaml
logging:
  pattern:
    console: '{"time":"%d","level":"%p","logger":"%c","msg":"%m"}%n'
```
 
### Actuator — Enabled Endpoints
 
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, info, metrics, prometheus
  endpoint:
    health:
      show-details: when-authorized
```
 
---
 
## 12. CI/CD and Deployment
 
### Pipeline (GitHub Actions — example)
 
```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with: { java-version: '21', distribution: 'temurin' }
      - run: ./mvnw verify
      - run: docker build -t spec-kit-springboot-example:${{ github.sha }} .
```
 
### Standard Dockerfile
 
```dockerfile
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```
 
---
 
## 13. Code Review Checklist
 
Before approving a Pull Request, verify:
 
- [ ] Code follows the defined naming conventions
- [ ] Test coverage exists for all new logic
- [ ] No credentials or secrets are committed to the repository
- [ ] New endpoints are documented in OpenAPI (see Section 7 — API Documentation)
- [ ] A DB migration is included if the schema changed
- [ ] Logs do not expose sensitive information (PII, passwords)
- [ ] The production `application.yml` uses environment variables
- [ ] Errors are handled through the global exception handler
- [ ] Feature branch references this constitution in its planning artifacts
- [ ] `spec.md`, `plan.md`, and `tasks.md` are present and up to date under `.specify/templates/`
---
 
*This document is a living standard — any changes must be team-approved, versioned, and recorded with a new `Last Amended` date.*
 
**Version**: 1.0.0 | **Ratified**: 2026-05-21 | **Last Amended**: 2026-05-22