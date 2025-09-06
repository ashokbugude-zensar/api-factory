You will generate a complete, importable Node‑RED implementation and a matching OpenAPI 3.0 contract for the following specification.

TECH_DOC (authoritative; provided may specify requirements across different programming languages):


REQUIREMENTS (MANDATORY)

A) ARTIFACTS — RETURN THESE 2 FILES IN ORDER (each in its own fenced code block):
1) flows.json    (json)   → Node‑RED flows (importable, production‑ready)
2) openapi.json  (json)   → OpenAPI 3.0.x contract that EXACTLY matches the flows

B) NODE‑RED IMPLEMENTATION RULES
- Version: Node‑RED 3.x+ flow format.
- Contrib nodes (USE EXACTLY THESE node types and declare them in package.json):
  • JWT auth:            module `node-red-contrib-jwt`,       node type "jwt verify"
  • Redis (cache, idem): module `node-red-contrib-redis`,      node types "redis-config", "redis-command"
  • AJV validation:      module `@gregoriusrippenstein/node-red-contrib-validation-and-documentation`
    (place validators BEFORE business logic; throw catchable errors on failure)
  • PostgreSQL (if TECH_DOC specifies DB): module `node-red-contrib-postgresql`, node type "postgresql"
- Flow wiring pattern for protected endpoints:
  http in
    → Correlation (x-request-id + traceparent)
    → Extract Bearer
    → jwt verify (RS256 via JWKS URL ${JWT_JWKS_URL})
    → AJV validator (body, path, query, required headers)
    → Rate limiter (core delay rate-mode or contrib limiter)
    → Business logic (Function/DB/HTTP request as per TECH_DOC)
    → Response normalizer
    → http response
- Caching & idempotency:
  • GET /resource/{id}: read‑through Redis cache key `resource:{id}`, TTL `${RESOURCE_CACHE_TTL}` (or per TECH_DOC).
  • PUT/DELETE: invalidate relevant cache keys.
  • POST create: Redis idempotency via `SET idem:{Idempotency-Key} <serialized response> NX EX ${IDEMPOTENCY_TTL_SECONDS}`; if key exists, replay stored response.
- ETag & conditionals:
  • ETag format `"v{version}"`.
  • GET supports If‑None‑Match→304.
  • PUT requires If‑Match; on mismatch return 409; on success increment version and return new ETag.
- Pagination:
  • limit constrained (default from TECH_DOC, max ≤ 100 unless specified).
  • cursor is base64‑encoded structure; include RFC‑5988 Link header and X‑Total‑Count.
- Emitter:
  • POST batch endpoint (min/max from TECH_DOC) → async accept (202) and publish to Redis channel `${EMIT_CHANNEL}` (or publish target defined by TECH_DOC).
- Universal proxy:
  • /api/(.*) forwards to `${DOWNSTREAM_BASE_URL}` with original method, headers (Authorization, traceparent/tracestate, any acting‑as headers), and body.
- Observability & errors:
  • /healthz and /readyz
  • Global catch → normalized error envelope: `{ "error": { "code": "...", "message": "...", "details": {...} } }`
  • Provide node “info” docs for maintainers (purpose, inputs/outputs, env vars, error cases)
- Security baseline (document in README):
  • HTTPS and adminAuth in settings.js; secure runtime/editor (or reverse proxy).
  • No secrets in flows.json; use environment variables only (whole‑property `${VAR}` substitution).

C) OPENAPI 3.0 CONTRACT (openapi.json) — RULES
- Version: 3.0.3 (unless TECH_DOC mandates otherwise).
- Security: global bearerAuth (JWT) with RS256; describe required headers (Idempotency-Key, If‑Match, If‑None‑Match).
- Schemas: define request/response models per TECH_DOC (e.g., User, Create/Update requests, Error, Paging).
- Parameters: strict path/query validations (regex for IDs, `format: email`, phone regex if required, enums, ranges).
- Responses: exhaustive status codes (200/201/204/304/400/401/403/404/409/429 as applicable), headers (ETag, Link, X‑Total‑Count), and example payloads.
- Pagination: body `nextCursor` (nullable), headers `Link` and `X‑Total‑Count`.
- Optional vendor extensions to document runtime behavior:
  • x-idempotency { storage: "redis", key: "idem:{Idempotency-Key}", replayWindowSeconds: ${IDEMPOTENCY_TTL_SECONDS} }
  • x-cache { strategy: "read-through", key: "resource:{id}", ttlSeconds: ${RESOURCE_CACHE_TTL} }
- Ensure naming and types are friendly to Java/Spring (for OpenAPI Generator).

D) VALIDATIONS (AJV + Contract)
- Body: required fields; types; min/max lengths; `format: email` and phone regex if specified.
- Path: strict ID regex (e.g., `^usr_[a-z0-9]+$`) or TECH_DOC pattern.
- Query: limit range, cursor base64, filter enums.
- Headers: enforce Idempotency-Key on POST create; If‑Match on PUT; If‑None‑Match on GET (optional per TECH_DOC).
- Sanitization: strip control chars and trim strings before validation (document in node info/README).

E) PERSISTENCE (PostgreSQL) — REQUIRED if TECH_DOC specifies DB
- Module: `node-red-contrib-postgresql` (node type "postgresql")
- Connection: build `msg.pgConfig` per request from env vars:
  PGHOST, PGPORT, PGDATABASE, PGUSER, PGPASSWORD, PGSSL
- Queries: use parameterized SQL ONLY via `msg.query` + `msg.params` (or named params if supported).
- CRUD mapping example (adapt to TECH_DOC):
  • POST /...: INSERT ... RETURNING *; set initial `version=1`; compute ETag as `"v{version}"`.
  • GET /{id}: SELECT ...; honor If‑None‑Match → 304.
  • PUT /{id}: UPDATE ... SET ..., updated_at=NOW(), version=version+1
      WHERE id=$... [AND version=parsed(If‑Match)] RETURNING *; on mismatch → 409.
  • DELETE /{id}: DELETE WHERE id=$1; 204 on success, 404 if not found.
- Column mapping (example): first_name↔firstName, last_name↔lastName, created_at↔createdAt, updated_at↔updatedAt, version↔version.
- Indexing: ensure PK on id and index on created_at (document DDL in README).

F) ENVIRONMENT VARIABLES (MUST be in .env.example and used in flows.json)
- JWT_JWKS_URL
- REDIS_HOST, REDIS_PORT, REDIS_PASSWORD, REDIS_DB
- RESOURCE_CACHE_TTL (or domain‑specific e.g., USER_CACHE_TTL)
- IDEMPOTENCY_TTL_SECONDS
- DOWNSTREAM_BASE_URL
- EMIT_CHANNEL
- PGHOST, PGPORT, PGDATABASE, PGUSER, PGPASSWORD, PGSSL
- PORT (optional)

I) SELF‑CHECKS BEFORE YOU OUTPUT
1) Validate JSON syntax of flows.json, openapi.json, package.json, and .env.example.
2) Ensure flows.json imports cleanly (unique ids, valid z, wires present).
3) Confirm all non‑core node types are covered in package.json.
4) Verify one‑to‑one parity between openapi.json and flows.json (paths, methods, params, headers, schemas, statuses).
5) Ensure no secrets or literal credentials in flows.json.
6) Provide helpful node “info” descriptions for maintainers.
7) Confirm parameterized SQL only—no string concatenation.
8) Ensure Postgres/Redis/JWT/AJV nodes appear and are correctly wired per TECH_DOC.

OUTPUT FORMAT (STRICT)
Return ONLY the 2 files below, in this order, each in a fenced code block with the correct language hint:
1) flows.json    → ```json ... ```
2) openapi.json  → ```json ... ```

Do not add explanations or commentary outside these five blocks. All files must be complete, validated and ready to use.