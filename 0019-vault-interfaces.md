## Vault Interfaces 

## Prerequisite Mental Model — Vault Is an API System First

Vault is NOT primarily:

• A CLI tool  
• A Web UI application  

Vault is:

✔ A server exposing an HTTP API

All interactions flow through this API.

Even when you use CLI or UI → You are still using the API indirectly.

---

## Three Vault Interfaces

Vault provides three main interfaces:

✔ CLI (Command Line Interface)  
✔ UI (Web Interface)  
✔ HTTP API (Core Interface)

Each serves a different purpose.

---

## 1. Vault HTTP API (The Core Interface)

This is the most important interface.

Everything in Vault is implemented as:

✔ HTTP endpoints

Example:

/v1/secret/data/myapp  
/v1/auth/token/create  
/v1/sys/policies/acl  

---

## Why HTTP API Is the Most Powerful

✔ All Vault features available  
✔ No limitations  
✔ Automation friendly  
✔ Language agnostic  
✔ Infrastructure friendly  

If something cannot be done via CLI/UI → It can ALWAYS be done via API.

---

## Real-World Insight

Production systems primarily use:

✔ HTTP API

Because:

✔ Applications integrate programmatically  
✔ Infrastructure tools rely on it  

---

## Example API Call (Conceptual)

Read secret:

    GET /v1/secrets/data/myapp/config

Write secret:

    POST /v1/secrets/data/myapp/config

Authentication:

    POST /v1/auth/approle/login

---

## 2. Vault CLI (Thin Wrapper Over API)

Very important concept.

Vault CLI is NOT a separate system.

Vault CLI:

✔ Simply invokes HTTP API calls

Think of CLI as:

Human-Friendly API Client

---

## What Happens When You Run CLI Commands?

Example:

    vault kv get secrets/myapp/config

Internally:

✔ CLI builds HTTP request  
✔ Sends request to Vault API  
✔ Receives JSON response  
✔ Formats output  

---

## Why CLI Exists

✔ Easier for humans  
✔ Simplifies API complexity  
✔ No need to craft HTTP requests manually  

Ideal for:

✔ Administrators  
✔ Operators  
✔ Debugging  
✔ Learning  

---

## Critical Insight — CLI Limitations

Not all API features are fully exposed via CLI.

Why?

✔ CLI prioritizes usability  
✔ Some low-level operations remain API-only  

---

## 3. Vault UI (Web Interface)

Vault UI is designed for:

✔ Human interaction  
✔ Visual workflows  
✔ Exploration  
✔ Operational convenience  

---

## Important Rule — UI Must Be Enabled

Vault UI is NOT enabled automatically.

Must be enabled in config file:

    ui = true

Without this:

✔ UI unavailable  

---

## Why UI Is Optional

Vault is designed for:

✔ Automation  
✔ API-first usage  

UI is convenience layer, not requirement.

---

## What UI Actually Does Internally

Just like CLI:

✔ UI invokes HTTP API

Browser → UI → HTTP API → Vault Core

---

## Important Limitation — UI Feature Coverage

Not all Vault features are available via UI.

Why?

✔ UI focuses on common workflows  
✔ Advanced operations remain API/CLI  

---

## Critical Vault Principle — Everything Uses HTTP API

This is the most important takeaway.

Flow of interactions:

CLI → HTTP API  
UI → HTTP API  
Automation → HTTP API  

Vault = API Engine

Interfaces = Access Layers

---

## Authentication Across Interfaces (Critical Security Rule)

Vault NEVER allows anonymous access.

Regardless of interface:

✔ Authentication required

---

## Why Authentication Is Mandatory

Vault controls:

✔ Secrets  
✔ Encryption operations  
✔ Tokens  
✔ Identity  

Allowing unauthenticated access = Security disaster.

---

## Example — CLI Without Authentication

    vault kv get secrets/myapp/config

Vault Response:

Permission denied

---

## Example — UI Without Authentication

✔ Redirected to login screen

---

## Example — API Without Authentication

HTTP Response:

403 Forbidden

---

## How Authentication Works Conceptually

User / Machine:

1. Authenticate via auth method  
2. Vault validates identity  
3. Vault issues token  
4. Token used for subsequent requests  

This applies to:

✔ CLI  
✔ UI  
✔ HTTP API  

---

## Choosing the Right Interface — Logical Thinking

---

## For Humans / Learning / Debugging?

✔ CLI  
✔ UI  

---

## For Applications / Automation / Integrations?

✔ HTTP API  

---

## For Full Feature Access?

✔ HTTP API  

---

## Final Mental Model — Vault Interfaces

Vault = HTTP API System

CLI = Thin wrapper over API  
UI = Visual wrapper over API  

Authentication = Mandatory security boundary

Interfaces = Different doors to same system

---

## Critical Production Insights

✔ API is primary interface in real systems  
✔ CLI best for operators  
✔ UI best for visualization  
✔ UI must be enabled explicitly  
✔ Authentication always required  

---

## Summary

You now understand:

✔ Vault is API-first system  
✔ Three interfaces exist  
✔ HTTP API is the core  
✔ CLI/UI invoke API  
✔ UI is optional  
✔ Feature coverage differences  
✔ Authentication rules  

