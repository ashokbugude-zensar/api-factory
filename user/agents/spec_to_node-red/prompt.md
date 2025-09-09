# INPUT
You are provided a Technical documentation.

# TASK
You will generate a `flows.json` file that is:

- Fully importable into Node‑RED 4.1
- Deterministic, reproducible, and enterprise-grade
- Implements every requirement from the Technical documentation
- Production ready, error free, easily understandable

# SECURITY
- No secrets or credentials in flows.json
- Use `${VAR}` substitution for all environment variables

# OUTPUT
- A single file `flows.json`

# OUTPUT FORMAT
Return `flows.json` as a **pure JSON array**:
[
  ... all flow objects ...
]

- Output must be a **single JSON array** of Node-RED flow objects
- Do **not** wrap the flows in an object with `version` or `flows` keys
- Do **not** include any Markdown fencing like ```json or ```
- Do **not** include any commentary, explanations, or additional files

# REQUIRED BEHAVIOR 
Implement all of the following:
- JWT verification via JWKS.
- Do not create seperate api/flow/subflow for jwt verification for each api
- All apis should first implement JWT verification after being called and continue with regular flow on successful authentication 
- Validation for body, path, query, headers
- Validation of input parameters and body for all fields as mentioned in technical doc
- ETag handling for GET/PUT
- Pagination with limit, cursor, Link header, X-Total-Count
- Universal proxy for `/api/**`
- Rate limiting
- Correlation/tracing headers
- Health and readiness endpoints
- Global catch node → normalized error envelope
- PostgreSQL persistence with parameterized SQL only
- Error Handling should be reused by all apis
- Logging should be reused by all apis

# IMPLEMENTATION REQUIREMENTS
Implement all endpoints and behaviors specified in the Technical Documentation:
- POST /api/v1/users → Create user
- GET /api/v1/users/{id} → Retrieve user
- GET /api/v1/users → List users
- PUT /api/v1/users/{id} → Update user
- DELETE /api/v1/users/{id} → Delete user
- GET /healthz  → Health endpoints

# ENVIRONMENT VARIABLES
Use `${VAR}` substitution for all required environment variables

# DATABASE - Postgres
- Use db for all apis where applicable
- Use the `node-red-contrib-postgresql` module
- Use `"type": "postgresql"` for all PostgreSQL nodes
- Use parameterized SQL only (`msg.query` + `msg.params`)
- Map snake_case DB columns to camelCase JSON
- Enforce optimistic concurrency with version column

# Caching
- No caching

# Authentication
- Use jwt authentication for all apis except health check

# Logging
- Use Logging for all apis where applicable

# Error handling
- Use industry standard error handling where applicable

# OTHER REQUIREMENTS
- Do not use ajv node module
- If any instructions are missed from tech doc, pls implement in best possible way.