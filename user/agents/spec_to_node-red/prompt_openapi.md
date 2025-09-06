You will generate a complete matching OpenAPI 3.0 contract for the Node_red flows file


REQUIREMENTS (MANDATORY)

A) ARTIFACTS — RETURN THESE 2 FILES IN ORDER (each in its own fenced code block):
2) openapi.json  (json)   → OpenAPI 3.0.x contract that EXACTLY matches the flows

B) OPENAPI 3.0 CONTRACT (openapi.json) — RULES
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

C) SELF‑CHECKS BEFORE YOU OUTPUT
1) Validate JSON syntax of openapi.json
2) Verify one‑to‑one parity between openapi.json and flows.json (paths, methods, params, headers, schemas, statuses).

OUTPUT FORMAT (STRICT)
Return ONLY the 1 files below in a fenced code block with the correct language hint:
1) openapi.json  → ```json ... ```

Do not add explanations or commentary outside these five blocks. All files must be complete, validated and ready to use.