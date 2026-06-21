# Postman Collection v2.1 — JSON structure reference

The schema URL that marks a v2.1 collection (required, exact string):

```
https://schema.getpostman.com/json/collection/v2.1.0/collection.json
```

## Top-level shape

```json
{
  "info": {
    "name": "My API",
    "description": "Auto-generated from the codebase.",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [ /* folders and/or requests */ ],
  "auth": { /* collection-wide default auth */ },
  "event": [ /* collection-level pre-request/test scripts */ ],
  "variable": [ /* collection variables */ ]
}
```

## Items: folders vs requests

An `item` entry is a **folder** if it has its own `item` array, otherwise a **request**.

Folder:

```json
{
  "name": "Users",
  "description": "User resource endpoints.",
  "item": [ /* nested requests or folders */ ]
}
```

Request:

```json
{
  "name": "Create user",
  "request": {
    "method": "POST",
    "header": [
      { "key": "Content-Type", "value": "application/json" }
    ],
    "url": {
      "raw": "{{base_url}}/api/v1/users",
      "host": ["{{base_url}}"],
      "path": ["api", "v1", "users"]
    },
    "body": {
      "mode": "raw",
      "raw": "{\n  \"email\": \"user@example.com\",\n  \"password\": \"secret123\"\n}",
      "options": { "raw": { "language": "json" } }
    }
  },
  "response": [ /* saved examples */ ]
}
```

## URL object

Always parameterize the host with a variable. Break the URL into parts:

```json
"url": {
  "raw": "{{base_url}}/api/v1/users/:id?expand=profile",
  "host": ["{{base_url}}"],
  "path": ["api", "v1", "users", ":id"],
  "query": [
    { "key": "expand", "value": "profile", "disabled": false }
  ],
  "variable": [
    { "key": "id", "value": "1", "description": "User ID" }
  ]
}
```

- Path parameters use the `:name` form inside `path` and are described in `variable`.
- Optional query params: set `"disabled": true` so they don't fire by default but stay documented.

## Body modes

- **JSON**: `"mode": "raw"` + `"options": { "raw": { "language": "json" } }`. The `raw` value is a JSON-as-string.
- **URL-encoded form**: `"mode": "urlencoded"`, `"urlencoded": [{ "key": "...", "value": "..." }]`.
- **Multipart / file upload**: `"mode": "formdata"`, `"formdata": [{ "key": "file", "type": "file", "src": [] }, { "key": "field", "value": "x", "type": "text" }]`.
- **No body**: omit `body` (e.g. GET/DELETE).

## Auth

Collection-wide bearer auth referencing a variable:

```json
"auth": {
  "type": "bearer",
  "bearer": [{ "key": "token", "value": "{{access_token}}", "type": "string" }]
}
```

Public request overrides to no auth inside its `request` object:

```json
"request": { "auth": { "type": "noauth" }, "method": "POST", "...": "..." }
```

Other common types: `basic`, `apikey`, `oauth2`. Match the codebase's scheme.

## Response examples

Saved under a request's `response` array. Each example carries the request that produced it:

```json
"response": [
  {
    "name": "201 Created",
    "originalRequest": {
      "method": "POST",
      "header": [{ "key": "Content-Type", "value": "application/json" }],
      "url": { "raw": "{{base_url}}/api/v1/users", "host": ["{{base_url}}"], "path": ["api", "v1", "users"] },
      "body": { "mode": "raw", "raw": "{\"email\":\"user@example.com\"}" }
    },
    "status": "Created",
    "code": 201,
    "_postman_previewlanguage": "json",
    "header": [{ "key": "Content-Type", "value": "application/json" }],
    "body": "{\n  \"status\": \"success\",\n  \"data\": { \"id\": 1 }\n}"
  }
]
```

Match the `body` of examples to the API's real response envelope (read the response helpers/serializers).

## Variables

```json
"variable": [
  { "key": "base_url", "value": "http://localhost:8080", "type": "string" },
  { "key": "access_token", "value": "", "type": "string" }
]
```

## Events / test scripts

Collection- or request-level scripts use the `event` array. To capture a token returned by a login request and store it for later requests:

```json
"event": [
  {
    "listen": "test",
    "script": {
      "type": "text/javascript",
      "exec": [
        "const json = pm.response.json();",
        "const token = json.data && json.data.access_token;",
        "if (token) { pm.collectionVariables.set('access_token', token); }"
      ]
    }
  }
]
```

Use `pm.environment.set(...)` instead of `pm.collectionVariables.set(...)` if you generate a separate environment.

## Minimal complete template

```json
{
  "info": {
    "name": "Example API",
    "description": "Auto-generated.",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "auth": { "type": "bearer", "bearer": [{ "key": "token", "value": "{{access_token}}", "type": "string" }] },
  "variable": [
    { "key": "base_url", "value": "http://localhost:8080", "type": "string" },
    { "key": "access_token", "value": "", "type": "string" }
  ],
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Login",
          "request": {
            "auth": { "type": "noauth" },
            "method": "POST",
            "header": [{ "key": "Content-Type", "value": "application/json" }],
            "url": { "raw": "{{base_url}}/login", "host": ["{{base_url}}"], "path": ["login"] },
            "body": { "mode": "raw", "raw": "{\"email\":\"a@b.com\",\"password\":\"x\"}", "options": { "raw": { "language": "json" } } }
          },
          "event": [
            {
              "listen": "test",
              "script": { "type": "text/javascript", "exec": [
                "const t = pm.response.json().data.access_token;",
                "if (t) pm.collectionVariables.set('access_token', t);"
              ] }
            }
          ],
          "response": []
        }
      ]
    }
  ]
}
```
