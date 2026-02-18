## Vault Authentication Using HTTP API

## Prerequisite Mental Model — Vault API Authentication Basics

Vault API uses:

✔ HTTP requests  
✔ JSON payloads  
✔ Token-based authentication  

Every authentication request:

✔ Returns JSON response  
✔ Contains Vault token  

Vault does NOT manage sessions.

Tokens are the only access mechanism.

---

## What Does API Authentication Return?

Successful authentication response includes:

✔ client_token → The actual Vault token  
✔ accessor → Token reference ID  
✔ policies → Attached permissions  
✔ metadata → Identity information  
✔ TTL → Token lifetime  

---

## Example Authentication Response (Simplified)

    {
      "auth": {
        "client_token": "s.xxxxx",
        "accessor": "hvs.xxxxx",
        "policies": ["default"],
        "lease_duration": 3600,
        "renewable": true
      }
    }

---

## Critical Rule — Vault Does NOT Extract Token for You

When using API:

✔ You must parse JSON response  
✔ Extract client_token manually  
✔ Store/use token securely  

Vault provides raw response only.

---

## Why Vault Works This Way

Vault is designed for:

✔ Machines  
✔ Automation  
✔ Stateless communication  

Token extraction responsibility lies with client.

---

## Authentication Using an Auth Method

Vault authentication endpoints follow pattern:

    POST /v1/auth/<method>/login

Example AppRole:

    POST /v1/auth/approle/login

---

## Step 1 — Prepare Authentication Payload

Example `auth.json`:

    {
      "role_id": "1213453",
      "secret_id": "sdfojd923r"
    }

---

## Why JSON Payload?

Vault API expects:

✔ Structured request body  
✔ Method-specific credentials  

---

## Step 2 — Send Authentication Request

    curl \
      --request POST \
      --data @auth.json \
      https://vault.example.com:8200/v1/auth/approle/login

---

## What Happens Internally

Vault:

✔ Validates RoleID  
✔ Validates SecretID  
✔ Maps policies  
✔ Issues Vault token  

---

## Step 3 — Vault Returns JSON Response

Response includes:

✔ client_token  

Important:

✔ No automatic storage  
✔ No token helper  
✔ No caching  

Unlike CLI behavior.

---

## Step 4 — Extract Token (Critical Step)

Using jq:

    VAULT_TOKEN=$(curl \
      --request POST \
      --data @auth.json \
      https://vault.example.com:8200/v1/auth/approle/login \
      | jq -r '.auth.client_token')

---

## Why jq Is Commonly Used

Vault API responses are JSON.

jq provides:

✔ Reliable parsing  
✔ Clean extraction  
✔ Automation-friendly workflows  

---

## Token Handling Responsibility

After extraction:

✔ Store token securely  
✔ Use token for subsequent requests  

Vault does NOT manage token lifecycle client-side.

---

## Step 5 — Use Token for Vault Requests

Example reading secret:

    curl \
      --header "X-Vault-Token: s.xxxxx" \
      https://vault.example.com:8200/v1/secret/data/app01

---

## Critical Vault Principle — Every Request Needs Token

Authentication = Obtain token

Everything else = Token usage

Vault never switches modes.

---

## Demo Workflow — Step-by-Step

---

## Step 1 — Authenticate Using API

Example password-based login (conceptual):

    curl \
      --request POST \
      --data @password.json \
      http://127.0.0.1:8200/v1/auth/login/bryan@krausen.io

---

### Example password.json

    {
      "password": "mypassword"
    }

---

## Step 2 — Observe JSON Response

Vault returns:

✔ client_token  

Important reminder:

✔ Vault does NOT save token  
✔ Client must extract token  

---

## Step 3 — Extract Token Manually

    VAULT_TOKEN=$(curl \
      --request POST \
      --data @password.json \
      http://127.0.0.1:8200/v1/auth/login/bryan@krausen.io \
      | jq -r '.auth.client_token')

---

## Step 4 — Use Token to Access Secrets

    curl \
      --header "X-Vault-Token: $VAULT_TOKEN" \
      http://127.0.0.1:8200/v1/secret/data/app01

---

## What Happens Internally

Vault:

✔ Validates token  
✔ Checks TTL  
✔ Evaluates policies  
✔ Returns secret  

---

## Important Differences — API vs CLI Authentication

---

## CLI Authentication

✔ Token helper caches token  
✔ `.vault-token` file created  
✔ Easier for humans  

---

## API Authentication

✔ No caching  
✔ No helper  
✔ Manual parsing required  
✔ Designed for machines  

---

## Why API Does NOT Cache Tokens

Vault API is:

✔ Stateless  
✔ Client-agnostic  
✔ Storage-independent  

Token caching is client responsibility.

---

## Token Accessor — Advanced Concept

Authentication response also includes:

✔ accessor

Accessor allows:

✔ Token lookup  
✔ Token revocation  
✔ Auditing  

Without exposing token itself.

---

## Example Accessor Usage

Revoke token via accessor:

    POST /v1/auth/token/revoke-accessor

---

## Common Beginner Mistakes

---

### Forgetting X-Vault-Token Header

Vault returns:

✔ 403 Access Denied

---

### Incorrect JSON Payload

Vault returns:

✔ 400 Bad Request

---

### Wrong Endpoint Path

Vault returns:

✔ 404 Not Found

---

### Using HTTP Instead of HTTPS (Production)

✔ Major security risk  

Always use TLS in real environments.

---

## Critical Production Insights

✔ Always use HTTPS  
✔ Protect SecretIDs  
✔ Never log tokens  
✔ Use short-lived tokens  
✔ Rotate credentials  
✔ Parse JSON safely  
✔ Validate responses  

---

## Final Mental Model — API Authentication

Authentication Request → Vault Validates → Vault Issues Token → Client Extracts Token → Token Used for Requests

Vault = Token-Based Security Engine

Client = Responsible for Token Handling

---

## Summary

You now understand:

✔ Vault API authentication structure  
✔ JSON response contents  
✔ client_token extraction  
✔ jq parsing workflows  
✔ Token usage mechanics  
✔ API vs CLI differences  
✔ Token accessor purpose  
✔ Common errors  
✔ Security implications  
