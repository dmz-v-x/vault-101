## Configuring Auth Methods Using the Vault HTTP API 

## Prerequisite Mental Model — Vault API Basics

Vault exposes:

✔ REST-style HTTP endpoints

Base structure:

    http://<vault-address>:8200/v1/<path>

Important parts:

✔ HTTP Method (GET / POST / PUT / DELETE)  
✔ Headers (Authentication)  
✔ Payload (JSON Data)  

---

## Critical Requirement — Authentication Header

Every API call must include:

    X-Vault-Token

Example:

    X-Vault-Token: s.xxxxx

Without token:

✔ Vault returns 403 Access Denied

Vault never redirects.

---

## Why API Usage Matters

API is used by:

✔ Applications  
✔ Automation scripts  
✔ CI/CD systems  
✔ Infrastructure tools  

CLI/UI are convenience layers.

---

## Step 1 — Enabling an Auth Method via API

Auth methods are managed under:

    /sys/auth/

---

## API Endpoint Structure

    POST /v1/sys/auth/<path>

Example enabling AppRole:

    POST /v1/sys/auth/approle

---

## Example API Call

    curl \
      --header "X-Vault-Token: s.LGW34R98Uvosdnfsdwe43" \
      --request POST \
      --data @auth.json \
      http://127.0.0.1:8200/v1/sys/auth/approle

---

## What Happens Internally

Vault:

✔ Loads AppRole plugin  
✔ Mounts at approle/  
✔ Registers endpoints  

---

## What Is auth.json?

Payload defining auth method configuration.

Example:

    {
      "type": "approle",
      "description": "AppRole authentication"
    }

---

## Important API Insight

CLI command:

    vault auth enable approle

Internally calls:

✔ /sys/auth/approle

Same operation.

---

## Step 2 — Creating an AppRole via API

Once auth method is enabled:

✔ Configure using /auth/

---

## Endpoint Structure

    POST /v1/auth/<method>/role/<name>

Example:

    POST /v1/auth/approle/role/vaultcourse

---

## Example API Call

    curl \
      --header "X-Vault-Token: s98HSKD398hf438yhsodfh" \
      --request POST \
      --data @policies.json \
      http://127.0.0.1:8200/v1/auth/approle/role/vaultcourse

---

## Example policies.json Payload

    {
      "token_ttl": "20m",
      "token_max_ttl": "30m",
      "policies": ["vaultcourse"]
    }

---

## What Happens Internally

Vault:

✔ Creates AppRole → vaultcourse  
✔ Assigns policies  
✔ Configures token behavior  

---

## Critical Concept — Role vs Token

Role defines:

✔ Rules  
✔ TTL  
✔ Policies  

Token inherits:

✔ Permissions  

---

## Step 3 — Fetching Role ID

Every AppRole has:

✔ RoleID (public identifier)

---

## Endpoint Structure

    GET /v1/auth/approle/role/<name>/role-id

Example:

    GET /v1/auth/approle/role/vaultcourse/role-id

---

## Example API Call

    curl \
      --header "X-Vault-Token: sDFH038e09r203ru028rs" \
      http://127.0.0.1:8200/v1/auth/approle/role/vaultcourse/role-id | jq

---

## Why RoleID Exists

Applications use:

✔ RoleID + SecretID → Login

RoleID is safe to distribute.

---

## Sample Response

    {
      "data": {
        "role_id": "2f6a614c..."
      }
    }

---

## Why Use jq?

Vault API responses are JSON.

jq provides:

✔ Pretty formatting  
✔ Easy parsing  

---

## Step 4 — Generating Secret ID

SecretID acts as:

✔ Credential / Password equivalent

---

## Endpoint Structure

    POST /v1/auth/approle/role/<name>/secret-id

Example:

    POST /v1/auth/approle/role/vaultcourse/secret-id

---

## Example API Call

    curl \
      --request POST \
      --header "X-Vault-Token: sDFH038e09r203ru028rs" \
      http://127.0.0.1:8200/v1/auth/approle/role/vaultcourse/secret-id | jq

---

## Sample Response

    {
      "data": {
        "secret_id": "5e8d2b8f..."
      }
    }

---

## Critical Security Insight

RoleID → Public identifier  
SecretID → Sensitive credential  

Treat SecretID like:

✔ Password  
✔ API key  
✔ Secret  

---

## Complete Authentication Flow (Real Usage)

Application login request:

    POST /v1/auth/approle/login

Payload:

    {
      "role_id": "<role-id>",
      "secret_id": "<secret-id>"
    }

Vault returns:

✔ Client Token

---

## Why Vault Always Returns Tokens

Auth Methods → Validate Identity  
Vault Token → Grants Access  

Universal mechanism.

---

## Important API Design Principles

Vault API:

✔ Stateless  
✔ Token-based  
✔ No redirects  
✔ No sessions  
✔ Security-first  

---

## Common Beginner Errors

---

### Missing Token Header

Vault returns:

✔ 403 Access Denied

---

### Incorrect HTTP Method

Example:

Using GET instead of POST.

Vault returns:

✔ 405 Method Not Allowed

---

### Wrong Endpoint Path

Vault returns:

✔ 404 Not Found

---

## Final Mental Model — API Auth Configuration

Enable Auth Method → /sys/auth/

Configure Auth Method → /auth/

Fetch Credentials → /auth/

Authenticate → /auth/<method>/login

---

## Why API Knowledge Is Critical for Professionals

✔ Automation  
✔ CI/CD  
✔ Microservices  
✔ Cloud-native systems  
✔ Security tooling  

Real systems rarely rely on manual CLI usage.

---

## Critical Production Insights

✔ Always use TLS (https://)  
✔ Protect SecretIDs strictly  
✔ Use short TTL tokens  
✔ Avoid hardcoding tokens  
✔ Prefer machine auth patterns  
✔ Validate API responses  

---

## Summary

You now understand:

✔ Vault API structure  
✔ Authentication header usage  
✔ Enabling auth via API  
✔ Creating roles via API  
✔ Fetching RoleID  
✔ Generating SecretID  
✔ JSON payload mechanics  
✔ jq usage  
✔ Common errors  
