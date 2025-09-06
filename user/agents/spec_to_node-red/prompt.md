
# GENERATION PROMPT

You will generate a complete, importable Node‑RED implementation for the provided TECH_DOC.

## DELIVERABLE
Return only the following file:

- `flows.json` — Node‑RED 3.x+ flow format

## OUTPUT FORMAT (STRICT)
Return the file as a **pure JSON array**:

[
  ... all flow objects ...
]

Do **not** wrap the flows in an object with `version` or `flows` keys.
Do **not** include any Markdown fencing like ```json or ```.
Do **not** include any commentary, explanations, or additional files.

## IMPLEMENTATION REQUIREMENTS
Implement all endpoints and behaviors specified in the TECH_DOC:

- POST /api/v1/users → Create user (with Redis idempotency, PostgreSQL insert, ETag)
- GET /api/v1/users/{id} → Retrieve user (with Redis cache, ETag, If-None-Match)
- GET /api/v1/users → List users (with pagination, filters, Link header, X-Total-Count)
- PUT /api/v1/users/{id} → Update user (with If-Match, version bump, cache invalidation)
- DELETE /api/v1/users/{id} → Delete user (with cache invalidation)
- POST /api/emitter/v1/batch → Emit events to Redis channel `${EMIT_CHANNEL}`
- GET /healthz and /readyz → Health and readiness endpoints
- Universal proxy for `/api/**` → Forward to `${DOWNSTREAM_BASE_URL}`
- Global catch node → Normalized error envelope

## NODE TYPES
Use only the following contrib nodes:
- `jwt verify` from `node-red-contrib-jwt`
- `redis-config`, `redis-command` from `node-red-contrib-redis`
- AJV validator from `@gregoriusrippenstein/node-red-contrib-validation-and-documentation`
- `postgresql` from `node-red-contrib-postgresql`

## ENVIRONMENT VARIABLES
Use `${VAR}` substitution for:
- JWT_JWKS_URL
- REDIS_HOST, REDIS_PORT, REDIS_PASSWORD, REDIS_DB
- RESOURCE_CACHE_TTL
- IDEMPOTENCY_TTL_SECONDS
- DOWNSTREAM_BASE_URL
- EMIT_CHANNEL
- PGHOST, PGPORT, PGDATABASE, PGUSER, PGPASSWORD, PGSSL
- PORT (optional)

## VALIDATION
- AJV validation for body, path, query, headers
- Enforce Idempotency-Key on POST
- Enforce If-Match on PUT
- Enforce If-None-Match on GET
- Sanitize inputs (trim, strip control chars)

## DATABASE
- Use parameterized SQL only (`msg.query` + `msg.params`)
- Map snake_case DB columns to camelCase JSON
- Enforce optimistic concurrency with version column
