## Vault Authentication Using CLI 

## Prerequisite Mental Model — Vault Requires a Token

Vault follows a strict rule:

**Every request must include a valid token**

Authentication = Process of obtaining that token.

Tokens = Core identity mechanism.

---

## What Does “Vault Authentication” Actually Mean?

Vault authentication means:

✔ Proving identity  
✔ Vault validates identity  
✔ Vault issues token  

After this:

✔ Token used for all future requests

---

## Vault CLI Authentication Mechanisms

When using CLI, there are two main ways:

1. `vault login` command  
2. `VAULT_TOKEN` environment variable  

---

## Method 1 — Using `vault login`

Most common and beginner-friendly.

---

## Basic Token Login

    vault login s.xxxxx

Example:

    vault login s.sdfhshd09u340923jsldfsd

---

### What Happens Internally

Vault CLI:

✔ Sends token to Vault  
✔ Vault validates token  
✔ CLI stores token locally  

---

## Critical Concept — Token Helper

This is extremely important.

After successful login:

✔ CLI creates `.vault-token` file

Location:

User’s home directory.

Example:

    ~/.vault-token

---

## What Is Token Helper?

Token Helper:

✔ Local token cache  
✔ Prevents repeated login  
✔ Used automatically by CLI  

Think of it as:

Vault Session Cache

---

## Why Token Helper Exists

Without token helper:

✔ Must provide token for every command

Example:

    vault kv get secret/myapp --token=s.xxxxx

Very inconvenient.

---

## Token Helper Behavior

After login:

✔ Token saved in `.vault-token`

Future commands:

✔ CLI reads token automatically

No need to re-enter token.

---

## Token Helper Workflow (Mental Model)

1. Initial Authentication  
2. Vault returns token  
3. CLI writes `.vault-token`  
4. CLI reuses token  

---

## Important Rule — Login Only Once

After:

    vault login <token>

No need to login again unless:

✔ Token expires  
✔ Token revoked  
✔ File deleted  

---

## Method 2 — Login Using Auth Method

If you do NOT have a token:

Authenticate via auth backend.

Example:

    vault login -method=userpass username=bryan

Vault CLI prompts:

✔ Password  

---

### What Happens Internally

Vault:

✔ Validates credentials  
✔ Issues token  
✔ CLI caches token  

---

## Why This Matters

Most real workflows:

✔ Do NOT distribute tokens directly  
✔ Use identity systems instead  

---

## Example — Okta Login

    vault login -method=okta username=bryan@krausen.io

Vault prompts:

✔ Password  

Vault returns:

✔ Client Token  

---

## Important Insight — All Auth Methods Return Tokens

Regardless of method:

✔ userpass  
✔ ldap  
✔ okta  
✔ approle  
✔ aws  

Vault ALWAYS issues:

✔ Vault Token

Universal model.

---

## What Happens If You Skip Authentication?

Run command without token:

    vault kv get secret/myapp

Vault response:

✔ 403 Access Denied

Vault never redirects.

---

## Using `VAULT_TOKEN` Environment Variable

Used when:

✔ You already have token  
✔ Automation workflows  
✔ Scripts / CI/CD  

---

## Example

    export VAULT_TOKEN="s.xxxxx"

Now CLI commands automatically use:

✔ Environment token  

---

## Priority Rule — VAULT_TOKEN vs Token Helper

Vault CLI resolution order:

1. VAULT_TOKEN env variable  
2. `.vault-token` file  

Environment variable overrides helper file.

---

## Why This Override Exists

Allows:

✔ Temporary token usage  
✔ Script isolation  
✔ Context switching  

---

## Advanced Workflow — Parsing JSON Responses

Critical for automation.

---

## Why JSON Parsing Is Needed

Vault CLI can return structured output.

Set:

    export VAULT_FORMAT=json

Now Vault returns JSON instead of formatted text.

---

## Example — AppRole Login + Token Extraction

    export VAULT_ADDR="https://vault.example.com:8200"
    export VAULT_FORMAT=json

    OUTPUT=$(vault write auth/approle/login \
        role_id="1213453" \
        secret_id="sdfojd923r")

    VAULT_TOKEN=$(echo $OUTPUT | jq '.auth.client_token' -j)

    vault login $VAULT_TOKEN

---

## What Happens Here

Vault CLI:

✔ Returns JSON  
✔ jq extracts token  
✔ vault login caches token  

Used heavily in:

✔ Automation  
✔ Provisioning scripts  
✔ CI/CD pipelines  

---

## Why Not Directly Use OUTPUT?

Because:

✔ Token buried inside JSON structure  

Must extract `.auth.client_token`.

---

## Token Lifecycle & Expiration

Tokens may have:

✔ TTL (Time To Live)

If token expires:

✔ CLI commands fail  
✔ Must re-authenticate  

---

## Re-authentication Scenarios

You must login again if:

✔ Token expired  
✔ Token revoked  
✔ `.vault-token` deleted  
✔ Switching identities  

---

## Practical CLI Authentication Workflow

---

## Step 1 — Login Using Auth Method

    vault login -method=userpass username=bryan

Enter password.

Vault returns token.

---

## Step 2 — Token Helper Created

✔ `.vault-token` file generated

---

## Step 3 — Run Vault Commands

    vault auth enable aws
    vault kv get secret/myapp

No token required explicitly.

---

## Step 4 — Using Existing Token

If token already known:

    vault login s.xxxxx

---

## Critical Security Insights

✔ `.vault-token` is sensitive  
✔ Protect home directory  
✔ Never commit token file  
✔ Use short-lived tokens  
✔ Prefer auth backends over raw tokens  

---

## Common Beginner Mistakes

---

### Forgetting VAULT_ADDR

Vault CLI cannot locate server.

Fix:

    export VAULT_ADDR="http://127.0.0.1:8200"

---

### Token Expired Confusion

Commands suddenly fail.

Cause:

✔ TTL expiration

---

### Multiple Token Conflicts

VAULT_TOKEN overrides helper file.

---

## Final Mental Model — Vault CLI Authentication

Vault Authentication = Token Acquisition

vault login → Obtain token → Cache locally

Token Helper = Convenience + Usability Layer

VAULT_TOKEN = Automation + Override Mechanism

Vault = Token-Based Security System

---

## Summary

You now understand:

✔ What Vault authentication means  
✔ Why tokens are mandatory  
✔ vault login mechanics  
✔ Token helper behavior  
✔ `.vault-token` purpose  
✔ Auth method login  
✔ VAULT_TOKEN usage  
✔ Override priority  
✔ JSON parsing workflows  
✔ Re-authentication scenarios  
✔ Security implications  
