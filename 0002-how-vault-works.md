## How HashiCorp Vault Works

## Prerequisite Concepts You Must Understand First

Before learning Vault, you need to understand **three foundational ideas**. Vault is built entirely on these.

### 1. Authentication vs Authorization (Very Important)

These two words are often confused.

Authentication answers:
- Who are you?

Authorization answers:
- What are you allowed to do?

Vault strictly separates these two concepts.

You authenticate **once**  
You authorize **every request**

Vault works exactly like this.

---

### 2. Tokens (Temporary Identity)

A token in Vault is:
- A temporary identity
- Issued after successful authentication
- Used for all future requests

Important rule:
You do NOT authenticate again and again.  
You authenticate once → Vault gives you a token → You present the token for every request.

Tokens have:
- Permissions
- Expiry time (TTL)
- Policies attached

---

### 3. Paths (Everything in Vault Is a Path)

Vault is **path-based**.

Everything is accessed via a path:
- Secrets
- Auth methods
- Policies
- System configuration

If you understand paths, Vault becomes much simpler.

---

## Ways to Interact with Vault

Vault provides **multiple interfaces** to interact with it.

### 1. CLI (Command Line Interface)

Used by:
- Humans
- Scripts
- Automation tools

Examples:
- Developers testing locally
- Admins configuring Vault
- CI/CD pipelines

CLI internally talks to Vault using the HTTP API.

---

### 2. API (HTTP API)

Used for:
- Machine-to-machine communication
- Applications
- Microservices

Important fact:
The CLI and UI both use the same API internally.

Vault is API-first.

---

### 3. UI (Web Interface)

Used mainly by:
- Humans
- Administrators
- Security teams

The UI is helpful for:
- Visualizing policies
- Managing secrets
- Debugging access issues

---

## Vault Architecture (High-Level Flow)


::contentReference[oaicite:0]{index=0}


At a high level, Vault works in the following sequence.

---

## Step-by-Step: How a Request Flows Through Vault

### Step 1. User or Application Wants to Access Vault

This could be:
- A human user
- An application
- A CI/CD pipeline
- A cloud service

At this point, Vault does NOT know who you are.

---

### Step 2. Authentication to Vault

The client authenticates using an **authentication method**.

Common authentication methods include:

#### 2.1 Username and Password
- Simple
- Mostly for humans
- Not recommended for production automation

#### 2.2 RoleID and SecretID (AppRole)
- Designed for machines
- Very common in production
- Secure and auditable

#### 2.3 TLS Certificates
- Strong cryptographic identity
- Often used in enterprise environments

#### 2.4 Integrated Cloud Credentials
- AWS IAM
- Azure Managed Identity
- GCP Service Accounts

Vault validates these credentials **externally or internally**, depending on the auth method.

---

### Step 3. Vault Issues a Token

If authentication succeeds:
- Vault generates a token
- Token has a TTL (Time To Live)
- Token has policies attached

Important:
Vault does NOT store your password or credentials.
It only issues a token.

---

### Step 4. Token Is Used for All Requests

From now on:
- You present the token
- You do NOT authenticate again

Every request to Vault includes:
- The token

---

### Step 5. Vault Validates the Token

For every request, Vault checks:

1. Token exists
2. Token is valid
3. Token is not expired
4. Token has required permissions (policies)

If any check fails → request is denied.

---

### Step 6. Accessing a Vault Path

If token validation succeeds:
- Vault evaluates the requested path
- Checks policy permissions
- Returns data if allowed

If not allowed:
- Access is denied

---

## Vault Paths Explained (Very Important Concept)

A Vault path is a **hierarchical location** inside Vault.

Think of it like:
- A filesystem path
- Or a URL structure

Everything in Vault lives under a path.

---

## Common Types of Vault Paths

### 1. Secret Paths

These paths store **actual secret data**.

Example:
secret/data/myapp/config

Breakdown:

#### secret/
- This is the mount point
- Represents a secrets engine
- Default is KV (Key-Value)

#### data/
- Used only in KV version 2
- Indicates versioned data access

KV Versions:
- KV v1 → no data/ path
- KV v2 → uses data/ and metadata/

#### myapp/config
- Logical organization
- myapp is the application namespace
- config is the secret name

---

### 2. Auth Paths

Used to configure authentication methods.

Examples:
auth/aws/config  
auth/github/map/users/alice  

These paths:
- Configure login behavior
- Map identities to policies
- Define authentication rules

No secrets are stored here.

---

### 3. System Paths

Used for Vault’s internal management.

Important system paths include:

#### sys/policies/acl/
- Manage ACL policies
- Define permissions

#### sys/mounts/
- Enable or disable secrets engines
- View mounted engines

#### sys/auth/
- Enable or manage auth methods

These paths are restricted to highly privileged tokens.

---

## Missing but Critical Vault Components (Often Overlooked)

To fully understand Vault, you must also know these core components.

### 1. Storage Backend

Vault does NOT store data in memory.

It uses a backend such as:
- Consul
- Integrated Raft
- Cloud storage backends

All data is encrypted before storage.

---

### 2. Encryption Barrier

Vault uses an encryption barrier:
- Data is encrypted before writing
- Decrypted only in memory
- Protected by unseal keys

This is fundamental to Vault’s security model.

---

### 3. Policies

Policies define:
- What paths can be accessed
- What operations are allowed

Policies are attached to tokens.

No policy → no access.

---

### 4. Lease and TTL System

Vault uses leases:
- Secrets can expire
- Tokens expire
- Credentials auto-revoke

This enables zero-trust security.
