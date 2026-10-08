# Public API policy

This project is a **browser-facing** Good Heart Tech tool on a public hostname (for example `*.nonprofittools.org` or a related GHT domain).

## No public integrator API

- There is **no** supported public REST API for third-party automation.
- **Do not** expose anonymous **`/api/docs`** or **`/api/openapi.json`** on the public internet.
- Same-origin browser requests for the UI are expected; abuse-prone endpoints should use rate limits or Turnstile where applicable.

## Engineering

Internal OpenAPI artifacts (if any) belong in the repo under `openapi/` for agents and release export — not on the public hostname without access controls.

Standard: [OpenAPI API standard (GHT)](../openapi-api-standard.md)

