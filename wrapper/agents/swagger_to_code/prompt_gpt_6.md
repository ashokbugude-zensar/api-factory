# Generic Enterprise API Generation Prompt (XLSX-driven)

**Goal**: Generate a complete, production‑ready, cloud‑native application that exactly implements an OpenAPI 3.0 specification and a companion XLSX of business/technical requirements—achieving full compliance across architecture, security, logging, resilience, testing, deployment, and documentation, with explicit alignment to any provided comparison/analysis report.

---
## 1) Inputs
- **OpenAPI 3.0** spec (YAML/JSON) for the public REST contract.
- **XLSX requirements** file. Parse the columns **Questions**, **Answer**, **Remarks for Answer Selected**. Treat the XLSX as the single source of truth for:
  - Technologies & versions, architectural style, package naming
  - Endpoints, headers, methods, parameters, DTOs, validation rules
  - Security (authN/Z, headers, mTLS), secret management
  - Logging/observability/metrics/tracing
  - Resilience (timeouts, retries, circuit breakers, fallbacks)
  - Caching, performance, rate limiting expectations
  - Profiles, environment-specific configuration and deployment targets
  - Testing frameworks, tools, coverage thresholds
  - Documentation depth and delivery
- **(Optional) Comparison/Analysis report (Markdown)**: If provided, mine it for gaps and acceptance criteria; treat them as **mandatory**.

> If the XLSX omits any detail, select **sensible enterprise defaults** (documented in **README.md** under “Assumptions & Defaults”) and implement them fully—**no placeholders or TODOs**.

---
## 2) Output Format (strict)
- Output the entire application as a single JSON object.
- Each key in the JSON object must be the relative file path with paths as mentioned in input doc.
- Each value must be the complete content of that file as a string.
- Do not include any extra text, explanations, or markdown—only the JSON object.
- Include **all** generated files (sources, resources, test code, scripts, manifests) with **no extra commentary** outside the JSON object.
- **When giving the JSON output, do not include any ```json or ``` code fences — just output the raw JSON object as specified.**
- Example:
  {
    "src/main/java/com/demo/customer/controller/v1/CustomersysController.java": "package com.demo.customer.controller.v1;\n// ...rest of the code...",
    "pom.xml": "<project>...</project>",
    "docker/Dockerfile": "FROM openjdk:21-jdk-slim\n..."
  }

Top-level must include at least:
- `pom.xml` (or build file per XLSX), `.gitignore`, `README.md`, `Dockerfile`, `docker-compose.yml` (if applicable), `k8s-manifests/` (Deployment, Service, ConfigMap, Secret, HPA, Ingress as applicable), CI pipeline file (Jenkins/GitLab per XLSX), `formatter/`.
---

## 3) Technology & Architecture
- **Select languages/frameworks/versions strictly from XLSX**. Examples: Java version, Spring Boot/WebFlux/MVC, MapStruct/Lombok, logging libs, tracing/metrics, build tool.
- **Base package** and **artifact name** must match XLSX.
- Follow the **architecture style** from XLSX (e.g., layered/controller→service→gateway).
- **Versioning strategy** (URI/header/param) and package versioning (e.g., `controller/v1`, `service/v1`) come from XLSX. Implement routing accordingly.

**Minimum structure (adapt to XLSX)**
```
src/main/java/{{BasePackage}}/
  config/
  controller(/v1 ...)/
  service(/v1 ...)/
  gateway/                # external service clients (SOAP/REST/etc.)
  model/                   # DTOs and domain models
  exception/
  common/                  # constants, headers
  utils/
  MainApplication.java
src/main/resources/
  application.properties + profile-specific files (from XLSX)
  logging configs (e.g., logback*.xml) as per XLSX
k8s-manifests/
  deployment.yaml, service.yaml, configmap.yaml, secret.yaml, hpa.yaml, ingress.yaml (as required)
```
---
## 4) Endpoints & Business Logic
- Implement **every endpoint** in the OpenAPI spec with exact paths, methods, parameters, headers, request/response schemas, and status codes.
- Apply **header extraction and validation** per XLSX (required/optional, ranges/patterns). Ensure **null-safe** handling and defaults where specified.
- If XLSX calls for **SOAP integration**, implement a production‑grade SOAP client:
  - Build envelopes dynamically (JAXB or XML builder), inject headers (including any **custom enterprise headers named in XLSX**), map OpenAPI → SOAP request.
  - **mTLS / custom headers**: inject from secrets/config as per XLSX.
  - Parse SOAP responses/faults → map to DTOs/errors.
- Implement **caching**, **pagination**, **batch/rate constraints** if required by XLSX.
- Enforce **input validation** (JSR‑380), sanitization (XSS/SQLi prevention as applicable), and **business rule validation** where stated.

---

## 5) DTOs & Mapping
- Create DTOs exactly as defined by OpenAPI + XLSX field definitions (names, types, nullability, formats).
- Use **mapping strategy** from XLSX (e.g., MapStruct). If unspecified, implement clean manual mappers.
- Include **builders**/immutability per XLSX (e.g., Lombok `@Builder` or manual builders).

---
## 6) Security (Authentication, Authorization, Secrets)
- Implement **auth strategy** from XLSX: custom headers, API keys, OAuth2/JWT, Basic, or mTLS.
- **Authorization**: role/attribute-based per XLSX. If unspecified, secure all non‑health endpoints and allow health/metrics per XLSX guidance.
- **Actuator security** and management port exposure according to XLSX.
- **Secrets**: integrate with secret manager defined in XLSX (e.g., GCP Secret Manager). Fall back to environment variables only if XLSX allows. Never hardcode secrets.

---
## 7) Logging, Tracing & Metrics
- Implement **structured JSON logging** with fields: timestamp, level, thread, logger, message, traceId/correlationId, requestId, user/app id, http method, path, status, latency.
- Configure logging framework per XLSX (e.g., Logback + JSON encoder or cloud‑native logging starters). Include `logback-access-spring.xml`/`logback.xml` if applicable.
- Add **request/response** and **performance** logging (sanitize sensitive data).
- **Tracing**: propagate correlation/trace ids (MDC), integrate with tracing library per XLSX.
- **Metrics**: expose actuator metrics and custom counters/timers (request counts, response times, error rates, external call durations). Expose `/actuator/prometheus` if required.

---
## 8) Resilience & Fault Tolerance
- Configure **timeouts**, **retries**, **circuit breakers** using the library mandated by XLSX (e.g., Resilience4j). Include fallback strategies if specified.
- **SOAP/External faults**: map to meaningful API errors with clear messages and remediation hints.
- Publish **resilience metrics** (retry attempts, breaker states, fallback usage).

---
## 9) Exception Handling & Error Model
- Provide a **global exception handler** (`@RestControllerAdvice`) returning a structured error body with: timestamp, status, error, message, path, correlationId, and optional errorCode.
- Implement a **custom exception hierarchy** per XLSX (domain/system/integration/security/etc.).
- **Error mapping**: translate backend/transport errors → HTTP statuses as specified in XLSX.

---
## 10) Configuration & Profiles
- Generate **profile-specific properties** and configuration files for **every profile enumerated in XLSX**. If not specified, include `dev`, `qa`, `uat`, `prod` as a minimum.
- Externalize all endpoint URLs, credentials, feature flags, cache TTLs, timeouts, breaker settings.
- Support **region/tenant/division** patterning if present in XLSX.

---
## 11) Build, Packaging & Deployment
- Build tool and plugins strictly from XLSX (e.g., Maven with compiler, surefire/failsafe, jacoco, formatter, springdoc, resilience, logging, security).
- **Dockerfile**: multi‑stage build, non‑root user, healthcheck, JVM optimizations, optional JMX/exporter per XLSX. Keep image minimal and secure.
- **docker-compose.yml**: provide local infra mocks if XLSX requests (e.g., WireMock, local stack).
- **Kubernetes manifests**: Deployment, Service, ConfigMap, Secret, HPA, Ingress configured per XLSX. Mount secrets/configs properly; set resource requests/limits; liveness/readiness probes.
- **CI/CD**: Generate Jenkinsfile / `.gitlab-ci.yml` per XLSX with stages (build → test → scan → package → deploy) and quality gates.

---
## 12) Health, Liveness, Readiness
- Expose `/actuator/health`, and separate **liveness** and **readiness** either via actuator groups or custom indicators as per XLSX.
- Add **custom health indicators** for external dependencies (e.g., SOAP endpoint reachability, disk space, DB if any), and export health metrics.

---
## 13) Testing Requirements
- Implement **unit**, **integration**, **controller** and (if applicable) **contract** tests using frameworks from XLSX (e.g., JUnit 5, Mockito, WireMock, REST Assured, Testcontainers).
- Achieve **coverage threshold** from XLSX via **JaCoCo** (or tool specified). Include reports.
- Provide **sample payloads** under `src/test/resources/` and test utilities (JSON utils, header builders).

---
## 14) Documentation
- Provide **OpenAPI 3** documentation aligned to implemented endpoints with examples, headers, and error responses. Use the doc framework from XLSX (e.g., Springdoc). Serve UI at `/swagger-ui.html` or per XLSX.
- Generate a comprehensive **README.md**: setup, run, build, profiles, configuration, security, endpoints, examples, architecture diagram/overview, observability, CI/CD, assumptions & defaults, and operations (runbooks/alerts if specified).

---
## 15) Compliance Self‑Check (aligns with enterprise comparison criteria)
Ensure the generated project **fully satisfies** these categories (document proofs in README):
1. Code structure & modularity
2. DTO mapping strategy
3. Exception handling completeness
4. Logging implementation (structured, correlated, perf)
5. API documentation quality
6. Security (authN/Z, secrets, actuator)
7. Health/liveness/readiness
8. Versioning strategy & compatibility
9. Test coverage & quality
10. Deployment configuration (Docker/K8s/CI)

For each, list **where** in the repo it’s implemented and sample references (files/classes/links) in README.

---
## 16) Important Constraints
- **No placeholders** or TODO comments—provide complete, working implementations.
- Respect **file and path naming** conventions provided by XLSX (artifact name, base package, env profile names, etc.).
- When XLSX mandates a specific library (e.g., GCP Secret Manager, Logback JSON), **implement end‑to‑end**, including configs and wiring.
- Sanitize logs; never leak secrets.

---
## 17) Conditional Behavior Examples (apply only if XLSX requires)
- **SOAP**: Use `WebServiceTemplate` (or approved SOAP client), JAXB models, SOAP faults → error mapping, mTLS, custom headers from secret manager, division‑based endpoint selection.
- **Caching**: `@Cacheable` + TTL manager + deterministic keys, cache metrics.
- **Rate limiting**: If delegated to gateway (per XLSX), surface headers and document behavior.
- **mTLS**: Load certs via keystores/volumes per XLSX; configure SSL contexts and trust.
- **Gateway exposure**: Align with ingress/host/path per XLSX.

---
## 18) Deliverables Checklist (must be present)
- Complete source code implementing all endpoints and features from OpenAPI + XLSX
- Build scripts and formatter configs (if required)
- Logging & tracing configs; metrics & health endpoints
- Dockerfile (+ compose if needed)
- Kubernetes manifests (env‑ready)
- CI pipeline definition
- Tests with coverage reports
- README with architecture and operations and code analysis report

---
## 19) Comparison Report Alignment (if provided)
When a **comparison/analysis Markdown** file is present:
1. **Parse gaps** and missing items listed (e.g., SOAP integration completeness, logging depth, security config, env-specific configs/K8s).
2. **Treat each gap as a mandatory acceptance criterion** and implement it fully.
3. Add a **README section: “Comparison Alignment”** that lists each gap and the exact files/classes that close it (e.g., `SecurityConfig.java`, `logback-access-spring.xml`, `SoapClient.java`, `k8s-manifests/configmap.yaml`).
4. Include **sample logs**, **OpenAPI screenshots/paths**, and **metrics endpoints** in README to provide verifiable evidence.
5. Provide a **self-test script or instructions** to validate these items locally (e.g., curl commands, WireMock scenarios, actuator checks).

This ensures the generated project passes automated or manual comparison with a **100% score**.
