
# GENERATION PROMPT

You will generate a complete, importable Node‑RED implementation for the provided TECH_DOC.

## DELIVERABLES
Return the following files:
- `flows.json` — Node‑RED 3.x+ flow format
- `package.json` — must list all required contrib nodes with pinned versions

## WHY IMPORT ERRORS OCCUR
Node-RED will reject flows.json if it contains node types like `redis out`, `postgressql` that:
- Are incorrectly named
- Are not supported by any installed contrib module
- Are missing from package.json

## REQUIRED MODULES
Ensure package.json includes:
- `node-red-contrib-jwt`
- `node-red-contrib-redis`
- `node-red-contrib-object-validation`
- `node-red-contrib-postgresql`

If generating any other modules, ensure they are not dependent on ajv

## OUTPUT FORMAT (STRICT)
Return `flows.json` as a **pure JSON array**:
[
  ... all flow objects ...
]

Do **not** wrap the flows in an object with `version` or `flows` keys
Do **not** include any Markdown fencing like ```json or ```
Do **not** include any commentary, explanations, or additional files

## IMPLEMENTATION REQUIREMENTS
Implement all endpoints and behaviors specified in the TECH_DOC:
- POST /api/v1/users → Create user
- GET /api/v1/users/{id} → Retrieve user
- GET /api/v1/users → List users
- PUT /api/v1/users/{id} → Update user
- DELETE /api/v1/users/{id} → Delete user
- POST /api/emitter/v1/batch → Emit events
- GET /healthz and /readyz → Health endpoints
- Universal proxy for `/api/**`
- Global catch node → normalized error envelope

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

## DATABASE
- Use parameterized SQL only (`msg.query` + `msg.params`)
- Map snake_case DB columns to camelCase JSON
- Enforce optimistic concurrency with version column
