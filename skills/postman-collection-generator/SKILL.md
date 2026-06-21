---
name: postman-collection-generator
description: Generate an importable Postman Collection (v2.1) automatically from an API codebase. Scans route definitions, extracts methods/paths/params/bodies/headers, groups endpoints into folders, adds request/response examples, and wires environment variables for base URL and auth tokens. Language- and framework-agnostic.
when_to_use: User asks to generate, build, export, or update a Postman collection (or environment) from the code; wants an importable API collection; says "make a Postman collection", "export endpoints to Postman", "generate Postman from routes/API", "create a Postman environment". Use whenever the deliverable is a Postman-importable JSON describing the API's endpoints.
---

# Postman Collection Generator

Produce a valid, importable **Postman Collection v2.1** JSON (and optionally a Postman Environment JSON) that mirrors the API defined in the codebase. The goal is a collection a developer can import and run immediately: organized folders, parameterized URLs, request bodies, auth wired through variables, and at least one saved example per endpoint.

This skill is **agnostic** to language, framework, and routing style. Discover the routing conventions by reading the code; never assume a specific stack.

## Core workflow

Work through these phases in order. Track them with the task tools if the API is large.

### 1. Scan routes

Find every place the codebase declares HTTP endpoints. Do not guess the framework — detect it from the files actually present.

- Locate route registrations, controller/handler annotations, router config, or API schema files (e.g. an existing OpenAPI/Swagger spec — if one exists, prefer it as the source of truth and skip manual extraction).
- Search broadly for HTTP-verb registrations and path-mounting calls; routers commonly nest a base prefix or version segment plus per-resource sub-routes. Reconstruct the **full path** by concatenating every mount prefix down to the leaf route.
- Note any middleware attached to a route or route group (auth, rate limiting, CORS, content-type guards) — these drive headers and the auth scheme.

Deliver a flat inventory: `METHOD  full/path  ->  handler  [middleware...]` before generating anything. Confirm the inventory looks complete; missing routes are the most common failure.

### 2. Extract metadata

For each endpoint, gather what a request needs:

- **Method** and **full path** (with the original path-parameter syntax preserved logically).
- **Path params** — convert framework syntax (`:id`, `{id}`, `<id>`, `[id]`, etc.) to Postman's `:id` form, and register each as a URL variable.
- **Query params** — from handler logic, validation schemas, or docs. Include as disabled-by-default rows with example values when optional.
- **Request body** — infer shape from the model/DTO/validation/serializer the handler binds to. Produce a realistic JSON (or form-data / urlencoded / file-upload) example, not an empty `{}`.
- **Headers** — `Content-Type` per body type, plus any required custom headers. Authorization is handled via the auth scheme (see step 6), not hardcoded.
- **Auth requirement** — whether the route is public or protected (from its middleware/guard).

### 3. Organize endpoints

Group into a folder hierarchy that matches how the API is structured:

- One folder per resource or logical area (e.g. by the top-level path segment after the version prefix).
- Mirror nested route groups as nested folders when the API nests them.
- Order folders and requests to follow a natural lifecycle where obvious (create → list → get → update → delete; auth/login before protected calls).
- Name each request with its action, not just the path (e.g. "Create user", "List users", "Login").

### 4. Generate the collection

Emit Collection v2.1 JSON. Follow `reference/collection-v2.1.md` for exact structure. Key rules:

- `info` block with a `name`, a `description`, and the v2.1 schema URL.
- Every URL uses variables for the host/base (`{{base_url}}`), never a hardcoded literal. Split the URL into `raw`, `host`, `path`, and `query`/`variable` arrays as the schema requires.
- Path params appear in the `url.variable` array.
- Bodies use the correct `body.mode` (`raw` + JSON language, `formdata`, `urlencoded`, or `file`).
- Protected requests inherit auth from the collection (do not paste tokens inline).

### 5. Add examples

For each request, save at least one **response example** (`response` array entry) so the collection is self-documenting:

- Include a representative success response (status code, status text, headers, and a JSON body matching the API's actual response envelope/shape — read the response helpers/serializers to match it exactly).
- Add an error example (e.g. 400/401/404) for endpoints where it's instructive.
- Examples store the originating request, so fill the example's `originalRequest` too.

### 6. Configure variables

Make the collection runnable across environments without edits to requests:

- Define collection-level `variable`s: at minimum `base_url`, plus `access_token` / `refresh_token` (or whatever the auth scheme uses) and any common IDs.
- Set the collection `auth` block to the detected scheme (usually `bearer` referencing `{{access_token}}`); public routes override with `auth: { type: "noauth" }`.
- Optionally emit a **separate Postman Environment JSON** (see `reference/environment.md`) holding the same keys with environment-specific values, so the user can switch between local/staging/prod.
- Where a login endpoint returns a token, add a small **test script** to that request that captures the token into the environment/collection variable automatically — this makes the protected requests work right after login.

## Output

- Write the collection to a sensible path (default `postman/<api-name>.postman_collection.json`); ask or infer the API name from the project.
- If you generate an environment, write `postman/<api-name>.local.postman_environment.json`.
- After writing, **validate**: the JSON must parse, and the top-level `info.schema` must be the v2.1 URL. Report the endpoint count and folder layout back to the user.
- Tell the user how to import (Postman → Import → File) and which variables to fill in.

## Detailed references

Load these as needed — do not inline their full content unless generating:

- `reference/collection-v2.1.md` — exact Collection v2.1 JSON structure: `info`, `item` (folders/requests), `request` (method/header/url/body/auth), `response` examples, `variable`, `event`/test scripts. Includes a minimal complete template.
- `reference/environment.md` — Postman Environment JSON structure and the token-capture test-script pattern.

## Quality bar

- The file imports into Postman with zero errors.
- Every endpoint in the route inventory appears exactly once.
- No secrets or live tokens are hardcoded — everything sensitive flows through variables.
- Bodies and examples reflect the API's real shapes, not placeholders.
