## Vault Architecture and Pathing Structure

# Part 1 — Vault Architecture

## Prerequisite Mental Model

Think of Vault as a **secure processing engine** sitting between:

Clients ↔ Vault ↔ Storage

Vault is NOT just storage.  
Vault is a **security enforcement system**.

---

## High-Level Vault Architecture


::contentReference[oaicite:0]{index=0}


At a high level, Vault consists of three major layers:

1. Client Interaction Layer
2. Vault Internal Core
3. Storage Backend

---

## 1. Client Interaction Layer

This is how things talk to Vault.

Vault exposes:

✔ HTTP API (Primary Interface)

Everything ultimately flows through:

**HTTP / HTTPS Requests**

Even:

- CLI → Uses API internally
- UI → Uses API internally

Vault is **API-first**.

---

### Who Uses This Layer?

✔ Human users  
✔ Applications  
✔ Automation tools  
✔ CI/CD pipelines  
✔ Cloud services  

Anything interacting with Vault sends API requests.

---

## 2. Storage Backend (Persistence Layer)

### What the Storage Backend Does

Storage backend provides:

✔ Persistent encrypted storage

Vault stores:

- Secrets
- Policies
- Tokens
- Metadata
- Leases

---

### Critical Security Principle

Vault encrypts data:

✔ Before writing to storage  
✔ After reading from storage  

Storage backend **never sees plaintext**.

Examples of storage backends:

- Integrated Raft (most common)
- Consul
- DynamoDB
- S3 (with DynamoDB)
- Filesystem (rare in production)
- In-memory (dev mode only)

---

## 3. Vault Core (The Brain of Vault)

This is where most confusion happens.

Vault Core is responsible for:

✔ Security enforcement  
✔ Request processing  
✔ Policy evaluation  
✔ Secret engine execution  
✔ Authentication  
✔ Auditing  

Vault Core is NOT storage.

Vault Core is **logic + security + routing**.

---

## Components Inside Vault Core

Let’s simplify each one.

---

### 1. Cryptographic Barrier (Extremely Important)

The barrier protects:

✔ All sensitive data  
✔ All secrets  
✔ All encryption keys  

Nothing crosses the barrier without:

✔ Valid token

This is Vault’s fundamental security model.

---

### 2. Policy Store

Stores:

✔ ACL Policies

Policies define:

- What paths can be accessed
- What operations are allowed

Every request is checked here.

---

### 3. Secrets Engines

Responsible for:

✔ Storing secrets  
✔ Generating secrets  
✔ Encrypting data  

This is Vault’s main functionality.

---

### 4. Authentication Methods

Responsible for:

✔ Validating identity  
✔ Issuing tokens  

No authentication → No token → No access.

---

### 5. Audit Devices

Responsible for:

✔ Logging requests and responses  

Security visibility layer.

---

### 6. System Backend

Responsible for:

✔ Internal Vault configuration  
✔ Managing policies  
✔ Enabling engines  
✔ Token management  

This is Vault’s administrative API.

---

### 7. Path Routing (Very Important)

Vault is **path-driven**.

Path routing decides:

✔ Which component handles a request

Vault reads your request path and routes internally.

Example:

Request → secret/data/app

Vault thinks:

“Ah, secret/ → Send to KV secrets engine”

---

## Final Vault Architecture Flow

Step-by-step:

1. Client sends HTTP request
2. Vault receives request
3. Token validated at barrier
4. Policy evaluated
5. Request routed via path
6. Component executes logic
7. Data encrypted/decrypted
8. Response returned
9. Audit log written

Vault is a **secure processing pipeline**.

---

# Part 2 — Vault Pathing Structure

Now we connect architecture with paths.

---

## Core Principle of Vault Paths

### 1. Everything in Vault Is Path-Based

In Vault:

✔ No direct component calls  
✔ No direct database queries  

Everything = Path

Vault behaves like a **path router + security engine**.

---

## 2. Path Prefix Determines Routing

Vault looks at the **first part of the path**.

Example:

secret/data/app

Vault reads:

secret/ → Secrets Engine

auth/aws/login

Vault reads:

auth/ → Auth Method

sys/policies/acl

Vault reads:

sys/ → System Backend

---

## 3. Mounting Components at Paths

Vault components are **mounted**.

Mount = Attach component to path

Examples:

Enable KV engine:

Mounted at → secret/

Enable database engine:

Mounted at → database/

Enable AWS auth:

Mounted at → auth/aws/

Mounting provides:

✔ Isolation  
✔ Logical separation  
✔ Flexibility  

---

## 4. Paths Depend on Enabled Features

Vault paths are dynamic.

Available paths depend on:

✔ Enabled secrets engines  
✔ Enabled auth methods  
✔ Enabled audit devices  

No engine → No path.

---

## 5. System Backend (Default Reserved Path)

Vault always has:

sys/

This path handles:

✔ Policies  
✔ Mount management  
✔ Token management  
✔ System configuration  

Cannot be removed.

---

## 6. Custom Paths Using --path Flag

Vault allows mounting components anywhere.

Example:

Enable KV engine at custom path:

my-secrets/

Instead of:

secret/

This enables:

✔ Environment isolation  
✔ Naming conventions  
✔ Multi-team usage

---

## 7. System Reserved Paths (Critical Concept)

Vault has reserved paths that:

✔ Cannot be removed  
✔ Cannot be reused  

Let’s explain each one.

---

### auth/

Purpose:

✔ Authentication method configuration

Examples:

auth/aws/  
auth/approle/  
auth/userpass/

Stores:

- Login endpoints
- Auth configuration
- Identity mappings

---

### cubbyhole/

Purpose:

✔ Private per-token storage

Characteristics:

- Each token gets isolated storage
- Only that token can access it

Used for:

✔ Temporary secret passing

---

### identity/

Purpose:

✔ Vault identity system

Manages:

- Entities (users/services)
- Groups
- Aliases

Critical for advanced identity workflows.

---

### secret/

Purpose:

✔ Default KV engine path (dev mode)

Important:

Not inherently special in production.  
Just a default mount.

---

### sys/

Purpose:

✔ Vault system backend

Used for:

- Policies
- Mount management
- Token management
- Configuration

Highly privileged operations.

---

## Final Mental Model for Paths

Vault paths are:

✔ Routing mechanism  
✔ Security boundary  
✔ Logical namespace  
✔ Component selector  

Vault = Path Router + Security Engine

---

## Final Summary

Vault Architecture provides:

- Secure barrier
- Policy enforcement
- Component routing
- Encrypted storage

Vault Paths provide:

- How requests are routed
- How components are isolated
- How Vault is structured

Together they define:

✔ How Vault operates internally  
✔ How clients interact with Vault  
