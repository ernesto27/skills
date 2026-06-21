# Postman Environment — JSON structure reference

A Postman Environment is a separate importable file holding variable values for one target (local, staging, prod). Requests reference these via `{{key}}`; switching environments swaps the values without touching requests.

## Structure

```json
{
  "name": "Example API — Local",
  "values": [
    { "key": "base_url", "value": "http://localhost:8080", "enabled": true },
    { "key": "access_token", "value": "", "enabled": true },
    { "key": "refresh_token", "value": "", "enabled": true }
  ],
  "_postman_variable_scope": "environment"
}
```

- `name` — shown in Postman's environment selector.
- `values[]` — each has `key`, `value`, and `enabled` (set `false` to keep a row but inactive).
- `_postman_variable_scope` — must be `"environment"`.

## Conventions

- Keep keys identical to the collection variables so the collection works with or without an environment selected.
- Leave secrets (`access_token`, `refresh_token`, API keys) blank — they get filled at runtime, typically by a login request's test script.
- Generate one environment per target the user needs (e.g. `*.local.postman_environment.json`, `*.staging.postman_environment.json`).

## Capturing tokens at runtime

When a login/auth endpoint returns a token, attach a test script to that request so subsequent protected requests authenticate automatically. With an active environment use `pm.environment.set`; without one, use `pm.collectionVariables.set`.

```javascript
const json = pm.response.json();
const access = json.data && json.data.access_token;
const refresh = json.data && json.data.refresh_token;
if (access) pm.environment.set("access_token", access);
if (refresh) pm.environment.set("refresh_token", refresh);
```

Adjust the JSON path (`json.data.access_token` above) to match the API's actual response envelope.
