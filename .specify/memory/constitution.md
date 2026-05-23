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

### General

| Field               | Value                                                  |
|---------------------|--------------------------------------------------------|
| Name                | `spec-kit-springboot-example`                          |
| Java Version        | 21                                                     |
| Spring Boot Version | 3.5                                                    |
| Build Tool          | Maven                                                  |
| Repository          | https://github.com/rroldan/spec-kit-springboot-example |
| Owner               | rroldan                                                |

### Technology Stack

| Layer              | Technology                     | Version  | Purpose                                         |
|--------------------|--------------------------------|----------|-------------------------------------------------|
| Language           | Java                           | 21       | Primary development language                    |
| Framework          | Spring Boot                    | 3.5      | Application framework                           |
| Web               | Spring Web MVC                 | (BOM)    | REST API layer                                  |
| **Security**       | **Spring Security + JWT**      | **(BOM / jjwt 0.12.6)** | **Authentication & role-based access control** |
| **Database**       | **PostgreSQL**                 | **16**   | **Primary relational database (all environments)** |
| ORM                | Spring Data JPA / Hibernate    | (BOM)    | Entity mapping and repository abstraction       |
| Migrations         | Flyway                         | (BOM)    | Schema versioning — Hibernate DDL is disabled   |
| API Docs           | SpringDoc OpenAPI (Swagger UI) | 2.5.0    | Interactive API documentation                   |
| **Testing**        | **Testcontainers + PostgreSQL**| **1.19+**| **Integration tests against a real DB container** |
| Testing            | JUnit 5 + Mockito              | (BOM)    | Unit and slice tests                            |
| Coverage           | JaCoCo                         | (BOM)    | Minimum 70% line coverage enforced in CI        |
| Observability      | Spring Actuator + Micrometer   | (BOM)    | Health, metrics, Prometheus endpoint            |
| Build / CI         | Maven + GitHub Actions         | —        | Build, test, and Docker image publication       |
| Containerisation   | Docker (eclipse-temurin:21)    | —        | Runtime image pushed to GHCR                   |

### Key Design Decisions

- **PostgreSQL only** — H2 and any other in-memory database are explicitly forbidden in every environment, including tests.
- **Testcontainers** — all integration tests that touch the database spin up a real `postgres:16-alpine` container via the shared `PostgresTestContainer` `@TestConfiguration`. The `@ServiceConnection` annotation (Spring Boot 3.1+) wires the container into the `DataSource` automatically.
- **Spring Security with JWT** — stateless sessions, two roles (`ROLE_USER` / `ROLE_ADMIN`), access enforced at both the `SecurityFilterChain` level and via `@PreAuthorize` on controllers. No HTTP Basic or form login is used.
- **Flyway owns the schema** — `spring.jpa.hibernate.ddl-auto` is set to `validate` in every environment. Schema changes MUST be delivered as versioned Flyway migration scripts.
- **Swagger UI disabled in `prod`** — enabled only in `dev` and `pre`.

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

  <!-- Database — PostgreSQL -->
  <dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
  </dependency>

  <!-- DB Migrations -->
  <dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
  </dependency>
  <dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-database-postgresql</artifactId>
  </dependency>

  <!-- Utilities -->
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
  </dependency>

  <!-- Testing — Testcontainers -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-testcontainers</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>postgresql</artifactId>
    <scope>test</scope>
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

Authentication is based on **JWT (Bearer Token)** managed by Spring Security. Every request to a protected endpoint MUST carry a valid `Authorization: Bearer <token>` header. Role-based access control (RBAC) is enforced at the HTTP security filter chain level and optionally reinforced with method-level annotations.

### Authentication Strategy

- JWT signed with **HS256** using a secret loaded from `${JWT_SECRET}`
- Access token expiry: **15 minutes**
- Refresh token expiry: **7 days**
- Passwords stored with **BCrypt** (strength = 12)

### Roles

| Role         | Spring authority | Description                              |
|--------------|-----------------|------------------------------------------|
| `ROLE_USER`  | `ROLE_USER`     | Authenticated end-user — read own data   |
| `ROLE_ADMIN` | `ROLE_ADMIN`    | Administrator — full CRUD on all resources |

### Endpoint Access Matrix

| Method | Path                        | `ROLE_USER` | `ROLE_ADMIN` | Public |
|--------|-----------------------------|:-----------:|:------------:|:------:|
| POST   | `/api/v1/auth/login`        |             |              | ✓      |
| POST   | `/api/v1/auth/register`     |             |              | ✓      |
| POST   | `/api/v1/auth/refresh`      |             |              | ✓      |
| GET    | `/actuator/health`          |             |              | ✓      |
| GET    | `/api/v1/users/me`          | ✓           | ✓            |        |
| GET    | `/api/v1/users/{id}`        |             | ✓            |        |
| GET    | `/api/v1/users`             |             | ✓            |        |
| POST   | `/api/v1/users`             |             | ✓            |        |
| PUT    | `/api/v1/users/{id}`        |             | ✓            |        |
| DELETE | `/api/v1/users/{id}`        |             | ✓            |        |

### Dependencies

```xml
<!-- Spring Security + JWT (jjwt) -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-api</artifactId>
  <version>0.12.6</version>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-impl</artifactId>
  <version>0.12.6</version>
  <scope>runtime</scope>
</dependency>
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-jackson</artifactId>
  <version>0.12.6</version>
  <scope>runtime</scope>
</dependency>

<!-- Test support -->
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-test</artifactId>
  <scope>test</scope>
</dependency>
```

### JWT Service

```java
@Service
public class JwtService {

    @Value("${jwt.secret}")
    private String secret;

    @Value("${jwt.expiration.access:900000}")    // 15 min in ms
    private long accessExpiration;

    @Value("${jwt.expiration.refresh:604800000}") // 7 days in ms
    private long refreshExpiration;

    private SecretKey key() {
        return Keys.hmacShaKeyFor(Decoders.BASE64.decode(secret));
    }

    public String generateAccessToken(UserDetails user) {
        return buildToken(user, accessExpiration);
    }

    public String generateRefreshToken(UserDetails user) {
        return buildToken(user, refreshExpiration);
    }

    private String buildToken(UserDetails user, long expiration) {
        return Jwts.builder()
            .subject(user.getUsername())
            .claim("roles", user.getAuthorities().stream()
                .map(GrantedAuthority::getAuthority).toList())
            .issuedAt(new Date())
            .expiration(new Date(System.currentTimeMillis() + expiration))
            .signWith(key())
            .compact();
    }

    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    public boolean isTokenValid(String token, UserDetails userDetails) {
        return extractUsername(token).equals(userDetails.getUsername())
            && !isTokenExpired(token);
    }

    private boolean isTokenExpired(String token) {
        return extractClaim(token, Claims::getExpiration).before(new Date());
    }

    private <T> T extractClaim(String token, Function<Claims, T> resolver) {
        Claims claims = Jwts.parser().verifyWith(key()).build()
            .parseSignedClaims(token).getPayload();
        return resolver.apply(claims);
    }
}
```

### JWT Authentication Filter

```java
@Component
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = authHeader.substring(7);
        String username = jwtService.extractUsername(token);

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            if (jwtService.isTokenValid(token, userDetails)) {
                UsernamePasswordAuthenticationToken auth =
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                auth.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(auth);
            }
        }
        filterChain.doFilter(request, response);
    }
}
```

### Security Configuration

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity          // enables @PreAuthorize / @PostAuthorize
@RequiredArgsConstructor
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthFilter;
    private final AuthenticationProvider authenticationProvider;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(AbstractHttpConfigurer::disable)
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                // ── Public endpoints ─────────────────────────────────────────
                .requestMatchers(
                    "/api/v1/auth/**",
                    "/actuator/health",
                    "/swagger-ui/**",
                    "/api-docs/**"
                ).permitAll()

                // ── USER: own profile only ───────────────────────────────────
                .requestMatchers(HttpMethod.GET, "/api/v1/users/me").hasAnyRole("USER", "ADMIN")

                // ── ADMIN: full user management ──────────────────────────────
                .requestMatchers("/api/v1/users/**").hasRole("ADMIN")

                // ── Everything else requires authentication ──────────────────
                .anyRequest().authenticated()
            )
            .authenticationProvider(authenticationProvider)
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
            .exceptionHandling(ex -> ex
                .authenticationEntryPoint((req, res, e) ->
                    res.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Unauthorized"))
                .accessDeniedHandler((req, res, e) ->
                    res.sendError(HttpServletResponse.SC_FORBIDDEN, "Forbidden"))
            )
            .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12);
    }
}
```

### ApplicationConfig (UserDetailsService + AuthenticationProvider)

```java
@Configuration
@RequiredArgsConstructor
public class ApplicationConfig {

    private final UserRepository userRepository;

    @Bean
    public UserDetailsService userDetailsService() {
        return username -> userRepository.findByEmail(username)
            .map(user -> org.springframework.security.core.userdetails.User.builder()
                .username(user.getEmail())
                .password(user.getPassword())
                .roles(user.getRole().name())   // e.g. Role.USER → "ROLE_USER"
                .build())
            .orElseThrow(() -> new UsernameNotFoundException("User not found: " + username));
    }

    @Bean
    public AuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
        provider.setUserDetailsService(userDetailsService());
        provider.setPasswordEncoder(passwordEncoder());
        return provider;
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config)
            throws Exception {
        return config.getAuthenticationManager();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12);
    }
}
```

### Method-Level Security on Controllers

Use `@PreAuthorize` as a second layer of protection where fine-grained rules are needed:

```java
@RestController
@RequestMapping("/api/v1/users")
@RequiredArgsConstructor
@Tag(name = "Users")
public class UserController {

    private final UserService userService;

    /** Any authenticated user can read their own profile. */
    @GetMapping("/me")
    @PreAuthorize("isAuthenticated()")
    public ResponseEntity<ApiResponse<UserResponse>> me(
            @AuthenticationPrincipal UserDetails principal) {
        return ResponseEntity.ok(ApiResponse.success(
            userService.findByEmail(principal.getUsername())));
    }

    /** ADMIN only — list all users. */
    @GetMapping
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<ApiResponse<Page<UserResponse>>> list(Pageable pageable) {
        return ResponseEntity.ok(ApiResponse.success(userService.findAll(pageable)));
    }

    /** ADMIN only — get any user by ID. */
    @GetMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<ApiResponse<UserResponse>> getById(@PathVariable UUID id) {
        return ResponseEntity.ok(ApiResponse.success(userService.findById(id)));
    }

    /** ADMIN only — create a user. */
    @PostMapping
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<ApiResponse<UserResponse>> create(
            @RequestBody @Valid CreateUserRequest request) {
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(ApiResponse.success(userService.create(request)));
    }

    /** ADMIN only — delete a user. */
    @DeleteMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<Void> delete(@PathVariable UUID id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Auth Controller

```java
@RestController
@RequestMapping("/api/v1/auth")
@RequiredArgsConstructor
@Tag(name = "Authentication")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final UserDetailsService userDetailsService;
    private final JwtService jwtService;

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody @Valid LoginRequest request) {
        authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(request.email(), request.password()));
        UserDetails user = userDetailsService.loadUserByUsername(request.email());
        return ResponseEntity.ok(new AuthResponse(
            jwtService.generateAccessToken(user),
            jwtService.generateRefreshToken(user)
        ));
    }

    @PostMapping("/refresh")
    public ResponseEntity<AuthResponse> refresh(@RequestBody @Valid RefreshRequest request) {
        String username = jwtService.extractUsername(request.refreshToken());
        UserDetails user = userDetailsService.loadUserByUsername(username);
        if (!jwtService.isTokenValid(request.refreshToken(), user)) {
            return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();
        }
        return ResponseEntity.ok(new AuthResponse(
            jwtService.generateAccessToken(user),
            request.refreshToken()
        ));
    }
}
```

### Security Test Helpers

```java
// Unit test — mock authenticated user
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired MockMvc mockMvc;
    @MockBean UserService userService;

    @Test
    @WithMockUser(roles = "ADMIN")
    void adminCanListUsers() throws Exception {
        when(userService.findAll(any())).thenReturn(Page.empty());
        mockMvc.perform(get("/api/v1/users"))
            .andExpect(status().isOk());
    }

    @Test
    @WithMockUser(roles = "USER")
    void userCannotListAllUsers() throws Exception {
        mockMvc.perform(get("/api/v1/users"))
            .andExpect(status().isForbidden());
    }

    @Test
    void unauthenticatedRequestIsRejected() throws Exception {
        mockMvc.perform(get("/api/v1/users"))
            .andExpect(status().isUnauthorized());
    }
}

// Integration test — full JWT flow with Testcontainers
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
@Import(PostgresTestContainer.class)
class AuthIntegrationTest {

    @Autowired MockMvc mockMvc;
    @Autowired ObjectMapper objectMapper;

    @Test
    void loginReturnsJwtToken() throws Exception {
        LoginRequest login = new LoginRequest("admin@example.com", "Str0ng!Pass");

        String body = mockMvc.perform(post("/api/v1/auth/login")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(login)))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.accessToken").isNotEmpty())
            .andReturn().getResponse().getContentAsString();

        String token = objectMapper.readTree(body).get("accessToken").asText();

        // Use the token to call a protected endpoint
        mockMvc.perform(get("/api/v1/users")
                .header("Authorization", "Bearer " + token))
            .andExpect(status().isOk());
    }

    @Test
    void userRoleCannotAccessAdminEndpoints() throws Exception {
        String token = loginAndGetToken("user@example.com", "Str0ng!Pass");

        mockMvc.perform(get("/api/v1/users")
                .header("Authorization", "Bearer " + token))
            .andExpect(status().isForbidden());
    }

    private String loginAndGetToken(String email, String password) throws Exception {
        LoginRequest req = new LoginRequest(email, password);
        String body = mockMvc.perform(post("/api/v1/auth/login")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(req)))
            .andReturn().getResponse().getContentAsString();
        return objectMapper.readTree(body).get("accessToken").asText();
    }
}
```

### `application.yml` — JWT configuration

```yaml
jwt:
  secret: ${JWT_SECRET}
  expiration:
    access: 900000      # 15 minutes
    refresh: 604800000  # 7 days
```

---

## 9. Persistence

The database engine for this project is **PostgreSQL 16**. No other database engine is permitted in any environment.

### DataSource Configuration

```yaml
# application.yml (base — overridden per environment)
spring:
  datasource:
    url: ${DB_URL:jdbc:postgresql://localhost:5432/speckit}
    username: ${DB_USER:speckit}
    password: ${DB_PASSWORD:speckit}
    driver-class-name: org.postgresql.Driver
    hikari:
      maximum-pool-size: 10
      minimum-idle: 2
      connection-timeout: 30000
  jpa:
    database-platform: org.hibernate.dialect.PostgreSQLDialect
    hibernate:
      ddl-auto: validate        # schema is owned by Flyway, never by Hibernate
    show-sql: false
```

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

All schema changes are managed exclusively through Flyway. Hibernate `ddl-auto` MUST remain `validate` in every environment.

Migration scripts go in `src/main/resources/db/migration/` following this naming pattern:

```
V1__create_users_table.sql
V2__add_orders_table.sql
V3__add_index_on_email.sql
```

Example migration:

```sql
-- V1__create_users_table.sql
CREATE TABLE users (
    id          UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
    email       VARCHAR(255) NOT NULL UNIQUE,
    password    VARCHAR(255) NOT NULL,
    created_at  TIMESTAMPTZ  NOT NULL DEFAULT now()
);

CREATE INDEX idx_users_email ON users (email);
```

### Pagination

All list endpoints must support pagination:

```
GET /api/v1/users?page=0&size=20&sort=createdAt,desc
```

---

## 10. Testing

All integration tests that touch the database MUST use **Testcontainers with a real PostgreSQL container**. In-memory databases (H2, HSQLDB) are **not permitted** for any integration test.

### Minimum Coverage Requirements

| Layer      | Minimum Coverage |
|------------|-----------------|
| Service    | 80%             |
| Controller | 70%             |
| Repository | 60%             |

### Shared Testcontainer Configuration

Define a single reusable base class (or `@TestConfiguration`) so all integration tests share one container instance, avoiding repeated startup overhead.

```java
// src/test/java/com/example/app/support/PostgresTestContainer.java
@TestConfiguration(proxyBeanMethods = false)
public class PostgresTestContainer {

    @Bean
    @ServiceConnection                          // Spring Boot 3.1+ auto-wires DataSource
    PostgreSQLContainer<?> postgresContainer() {
        return new PostgreSQLContainer<>("postgres:16-alpine")
            .withDatabaseName("speckit_test")
            .withUsername("speckit")
            .withPassword("speckit")
            .withReuse(true);                   // reuse container across test runs (local)
    }
}
```

> `@ServiceConnection` (Spring Boot 3.1+) automatically overrides `spring.datasource.*` — no manual `@DynamicPropertySource` needed.

### Unit Test — Service Layer

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    UserRepository userRepository;

    @InjectMocks
    UserService userService;

    @Test
    void shouldReturnUserWhenExists() {
        UUID id = UUID.randomUUID();
        User user = new User(id, "jane@example.com");
        when(userRepository.findById(id)).thenReturn(Optional.of(user));

        UserResponse response = userService.findById(id);

        assertThat(response.email()).isEqualTo("jane@example.com");
    }

    @Test
    void shouldThrowWhenUserNotFound() {
        UUID id = UUID.randomUUID();
        when(userRepository.findById(id)).thenReturn(Optional.empty());

        assertThatThrownBy(() -> userService.findById(id))
            .isInstanceOf(ResourceNotFoundException.class);
    }
}
```

### Integration Test — Repository Layer (Testcontainers + PostgreSQL)

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)  // disable H2
@Import(PostgresTestContainer.class)
class UserRepositoryIntegrationTest {

    @Autowired
    UserRepository userRepository;

    @Test
    void shouldPersistAndRetrieveUser() {
        User user = new User();
        user.setEmail("jane@example.com");
        user.setPassword("hashed");

        User saved = userRepository.save(user);

        assertThat(saved.getId()).isNotNull();
        assertThat(userRepository.findByEmail("jane@example.com")).isPresent();
    }

    @Test
    void shouldEnforceUniqueEmail() {
        userRepository.save(buildUser("dup@example.com"));

        assertThatThrownBy(() -> {
            userRepository.saveAndFlush(buildUser("dup@example.com"));
        }).isInstanceOf(DataIntegrityViolationException.class);
    }

    private User buildUser(String email) {
        User u = new User();
        u.setEmail(email);
        u.setPassword("hashed");
        return u;
    }
}
```

### Integration Test — Full Stack (MockMvc + Testcontainers)

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
@Import(PostgresTestContainer.class)
@Transactional
class UserControllerIntegrationTest {

    @Autowired MockMvc mockMvc;
    @Autowired ObjectMapper objectMapper;
    @Autowired UserRepository userRepository;

    @Test
    void shouldCreateUserAndReturn201() throws Exception {
        CreateUserRequest request = new CreateUserRequest("new@example.com", "Str0ng!Pass");

        mockMvc.perform(post("/api/v1/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.status").value("success"))
            .andExpect(jsonPath("$.data.email").value("new@example.com"));
    }

    @Test
    void shouldReturn409WhenEmailAlreadyExists() throws Exception {
        User existing = new User();
        existing.setEmail("taken@example.com");
        existing.setPassword("hashed");
        userRepository.save(existing);

        CreateUserRequest request = new CreateUserRequest("taken@example.com", "Str0ng!Pass");

        mockMvc.perform(post("/api/v1/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isConflict())
            .andExpect(jsonPath("$.code").value("EMAIL_ALREADY_EXISTS"));
    }
}
```

### `application-test.yml`

```yaml
# src/test/resources/application-test.yml
spring:
  flyway:
    enabled: true          # run migrations against the test container
  jpa:
    show-sql: true

logging:
  level:
    org.testcontainers: INFO
    com.example.app: DEBUG
```

Activate it in test classes with `@ActiveProfiles("test")` or set `spring.profiles.active=test` in `src/test/resources/application.properties`.

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

The pipeline runs on **GitHub Actions**. Testcontainers requires Docker — the `ubuntu-latest` runner provides it out of the box. No external database service is needed in CI; the PostgreSQL container is started automatically by Testcontainers during the Maven test phase.

### Pipeline — `.github/workflows/ci.yml`

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # ── 1. Build, test, and verify ──────────────────────────────────────────────
  build:
    name: Build & Test
    runs-on: ubuntu-latest          # Docker daemon is available on this runner

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Set up Java 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: 'maven'

      # Testcontainers pulls the postgres:16-alpine image at test time.
      # Pre-pulling avoids download time counting against the test timeout.
      - name: Pre-pull PostgreSQL image
        run: docker pull postgres:16-alpine

      - name: Run tests (unit + integration)
        run: ./mvnw verify -B
        env:
          # Testcontainers reads DOCKER_HOST automatically on ubuntu-latest.
          # Set TESTCONTAINERS_REUSE_ENABLE only for local dev; keep false in CI.
          TESTCONTAINERS_REUSE_ENABLE: false

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: target/surefire-reports/

      - name: Upload coverage report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: target/site/jacoco/

  # ── 2. Build and push Docker image (main branch only) ───────────────────────
  docker:
    name: Docker Build & Push
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Set up Java 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: 'maven'

      - name: Build JAR (skip tests — already run)
        run: ./mvnw package -DskipTests -B

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            ghcr.io/rroldan/spec-kit-springboot-example:latest
            ghcr.io/rroldan/spec-kit-springboot-example:${{ github.sha }}
```

### JaCoCo Coverage Gate

Add to `pom.xml` to enforce minimum coverage and fail the build if thresholds are not met:

```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <executions>
    <execution>
      <id>prepare-agent</id>
      <goals><goal>prepare-agent</goal></goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals><goal>report</goal></goals>
    </execution>
    <execution>
      <id>check</id>
      <phase>verify</phase>
      <goals><goal>check</goal></goals>
      <configuration>
        <rules>
          <rule>
            <element>BUNDLE</element>
            <limits>
              <limit>
                <counter>LINE</counter>
                <value>COVEREDRATIO</value>
                <minimum>0.70</minimum>
              </limit>
            </limits>
          </rule>
        </rules>
      </configuration>
    </execution>
  </executions>
</plugin>
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
- [ ] Integration tests use Testcontainers with PostgreSQL — no H2 or in-memory database
- [ ] `application-test.yml` is present and activates the test profile
- [ ] Feature branch references this constitution in its planning artifacts
- [ ] `spec.md`, `plan.md`, and `tasks.md` are present and up to date under `.specify/templates/`

---

*This document is a living standard — any changes must be team-approved, versioned, and recorded with a new `Last Amended` date.*

**Version**: 1.3.0 | **Ratified**: 2026-05-21 | **Last Amended**: 2026-05-23