## Vault Components — The Core Building Blocks of HashiCorp Vault

## Prerequisite Mental Model

Before diving into components, remember this:

Vault is like a **secure operating system for secrets**.

Just like an OS has:
- Disk (storage)
- Applications (programs)
- Login system (authentication)
- Logs (auditing)

Vault has:
- Storage backend
- Secrets engines
- Auth methods
- Audit devices

Now everything becomes easier to reason about.

---

# 1. Storage Backends

## What Is a Storage Backend?

A **storage backend** is:

The place where Vault physically stores its data.

Important clarification:

Vault does NOT store secrets inside itself permanently in memory.  
Vault needs an external storage system.

Think of it as:

Vault = Brain  
Storage Backend = Hard Drive

---

## What Does the Storage Backend Store?

The storage backend stores:

- Encrypted secrets
- Encrypted metadata
- Policies
- Tokens
- Configuration data
- Leases

Everything Vault knows must be persisted here.

---

## Key Characteristics of Vault Storage

### 1. Storage Location Is Configurable

The storage backend is defined inside:

**Vault Configuration File**

You explicitly tell Vault:

"Store your data here."

Examples:
- Integrated Raft Storage
- Consul
- Cloud storage options

---

### 2. All Data Is Always Encrypted

Vault encrypts data:

- **In transit → Using TLS**
- **At rest → Using AES-256 encryption**

Critical understanding:

The storage backend never sees plaintext secrets.

Even if storage is compromised:
Data remains encrypted.

---

### 3. Not All Storage Backends Are Equal

Different storage backends provide different capabilities.

Some support:
- High Availability (HA)
- Replication
- Better backup tooling
- Operational simplicity

Examples:

**Integrated Raft**
- Built into Vault
- Very popular
- Simple setup

**Consul**
- External system
- Strong HA capabilities
- Requires additional infrastructure

---

### 4. One Storage Backend Per Vault Cluster

Very important rule:

✔ A Vault cluster MUST have a storage backend  
✔ A Vault cluster can have ONLY ONE storage backend

You cannot mix multiple storage backends.

Why?

Vault needs a single consistent source of persisted truth.

---

## Why Storage Backends Matter

Storage affects:

- High availability
- Disaster recovery
- Performance
- Operational complexity
- Failure scenarios

This is a major architectural decision.

---

# 2. Secrets Engines (The Heart of Vault)

## What Is a Secrets Engine?

A **secrets engine** is:

A Vault component that **stores, generates, or encrypts secrets**.

This is the primary reason Vault exists.

Without secrets engines → Vault has no purpose.

---

## Mental Model for Secrets Engines

Think of secrets engines as:

**Vault Applications / Modules**

Each engine performs a specific job.

Examples:

- KV Engine → Store secrets
- Database Engine → Generate DB credentials
- Transit Engine → Encrypt data
- PKI Engine → Issue certificates

---

## What Can Secrets Engines Do?

Secrets engines can:

✔ Store secrets  
✔ Generate secrets dynamically  
✔ Encrypt / decrypt data  
✔ Issue certificates  
✔ Rotate credentials  

---

## Dynamic Secrets (Extremely Important)

Many secrets engines can connect to external systems.

Example:

Database Secrets Engine:

Instead of storing a DB password:

Vault:
1. Connects to database
2. Generates new credentials
3. Returns them
4. Sets TTL
5. Revokes automatically

Result:

No long-lived passwords.

Major security improvement.

---

## Multiple Secrets Engines Allowed

Vault supports:

✔ Many secrets engines  
✔ Multiple engines of the same type  

Example:

You can have:

- secret/ (KV engine)
- database/ (DB engine)
- transit/ (Encryption engine)

Each isolated at its own path.

---

## Secrets Engines Are Path-Based

Secrets engines are:

✔ Enabled at a path  
✔ Completely isolated  

Example:

secret/data/app1  
database/creds/readonly  

All interactions happen via paths.

---

## Why Secrets Engines Matter

Secrets engines define:

- What Vault actually does
- Your security model
- Your automation capabilities

This is Vault’s core value.

---

# 3. Authentication Methods

## Why Authentication Exists

Before accessing secrets:

Vault must answer:

"Who are you?"

This is handled by **authentication methods**.

---

## What Is an Auth Method?

An **auth method** is:

A Vault component responsible for verifying identity and issuing tokens.

Vault supports many identity systems.

---

## What Do Auth Methods Do?

Auth methods:

✔ Validate identity  
✔ Assign policies  
✔ Issue Vault tokens  

Important:

Auth methods do NOT grant direct access.

They issue tokens.

Tokens + Policies → Control access.

---

## Fundamental Goal of All Auth Methods

Every auth method has ONE purpose:

**Obtain a Vault Token**

Vault is token-based.

No token → No access.

---

## Multiple Auth Methods Allowed

Vault allows:

✔ Many authentication methods  

Examples:

- userpass (humans)
- AppRole (machines)
- AWS IAM
- Kubernetes
- TLS certificates

Each for different use cases.

---

## Humans vs Machines

Auth methods are often categorized:

**Human Authentication**
- Username/password
- LDAP
- OIDC / SSO

**Machine Authentication**
- AppRole
- AWS IAM
- Kubernetes
- TLS certs

Vault adapts to modern infrastructure.

---

## Default Auth Method = Tokens

In a fresh Vault setup:

Tokens are the default authentication mechanism.

This is mostly for bootstrap/admin workflows.

---

## Why Auth Methods Matter

They define:

- Identity model
- Trust boundaries
- Security posture
- Automation design

---

# 4. Audit Devices (Security Visibility Layer)

## What Is an Audit Device?

An **audit device** is:

A Vault component that logs every request and response.

Vault is a security system → Visibility is mandatory.

---

## What Gets Logged?

Audit logs contain:

✔ Requests  
✔ Responses  
✔ Paths accessed  
✔ Token identity  
✔ Operation details  

---

## Log Format = JSON

Vault outputs logs in:

**Structured JSON Format**

Benefits:

- Machine-readable
- Easy integration with SIEM tools
- Searchable and analyzable

---

## Sensitive Data Protection

Vault NEVER logs secrets in plaintext.

Sensitive values are:

✔ Hashed before logging

This preserves:
- Security
- Compliance
- Privacy

---

## Multiple Audit Devices Recommended

Vault supports:

✔ Multiple audit devices

Examples:

- File logs
- Syslog
- External logging systems

Best practice:

Enable more than one.

Why?

Avoid losing audit trails.

---

## Critical Behavior: Safety Over Availability

If audit devices are enabled:

Vault requires logs to be written successfully  
BEFORE completing the request.

If logging fails → Request fails.

Why?

Vault prioritizes **security integrity** over uptime.

This is a deliberate design decision.

---

# Final Summary — Vault Component Mental Model

Vault is composed of:

✔ Storage Backend → Where encrypted data lives  
✔ Secrets Engines → What Vault actually does  
✔ Auth Methods → How identity is verified  
✔ Audit Devices → How activity is tracked  

Together they provide:

- Secure storage
- Dynamic secrets
- Identity-based access
- Full auditability
