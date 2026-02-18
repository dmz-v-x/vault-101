## Vault Configuration File

## Prerequisite Mental Model — What Is the Vault Configuration File?

Vault is primarily a **server process**.

When you start Vault using:

    vault server

Vault must read instructions describing:

✔ Runtime behavior  
✔ Storage configuration  
✔ Network settings  
✔ Security mechanisms  

Those instructions live in:

**Vault Configuration File**

Think of it as:

Vault’s Startup Blueprint

---

## Configuration File Formats

Vault supports two formats:

✔ HCL (HashiCorp Configuration Language) — Most common  
✔ JSON — Alternative format  

HCL is preferred because:

✔ Easier to read  
✔ Cleaner structure  
✔ Less verbose  

---

## How Vault Uses the Configuration File

Vault reads the file during startup.

Example:

    vault server -config /etc/vault.d/vault.hcl

Meaning:

Start Vault → Apply these settings

---

## Where Is the Configuration File Stored?

Common convention:

    /etc/vault.d/vault.hcl

Why `/etc`?

✔ Standard Linux config location  
✔ Easier operational management  
✔ Better permission control  

Important:

Vault does NOT require `/etc`.

You may store it anywhere.

---

## What Is Configured Inside the File?

This file defines **how Vault runs**, not **what Vault manages**.

---

## Storage Backend (Mandatory)

Vault MUST know where to persist encrypted data.

Example:

    storage "file" {
      path = "/opt/vault/data"
    }

---

### Why Storage Is Required

Vault stores:

✔ Secrets  
✔ Policies  
✔ Tokens  
✔ Metadata  

Without storage → Vault cannot function.

Vault will refuse to start.

---

## Listener(s) (Mandatory)

Listeners define how Vault accepts connections.

Example:

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

---

### Listener Parameters Explained

#### address

Defines:

✔ Client communication endpoint

---

#### 0.0.0.0 Meaning

Vault listens on:

✔ All network interfaces

Accepts connections from any IP.

---

#### tls_disable = "true"

Disables TLS encryption.

Acceptable ONLY for:

✔ Local labs  
✔ Learning environments  

Never in production.

---

## Cluster Listener (Important for HA)

Example:

    listener "tcp" {
      address         = "0.0.0.0:8200"
      cluster_address = "0.0.0.0:8201"
    }

---

### cluster_address Purpose

Used for:

✔ Node-to-node communication  
✔ HA clustering  
✔ Replication  

---

## TLS Configuration (Production Critical)

Example:

    listener "tcp" {
      address = "0.0.0.0:8200"

      tls_cert_file = "/etc/vault/tls/server.crt"
      tls_key_file  = "/etc/vault/tls/server.key"
    }

---

### Why TLS Matters

Vault handles highly sensitive data.

Plain HTTP = Major security risk.

TLS provides:

✔ Encryption in transit  
✔ Identity validation  
✔ Protection from interception  

---

## Seal Stanza (Defines Unseal Mechanism)

Seal stanza controls **master key protection**.

---

### Default Behavior (No Seal Stanza)

If seal stanza is absent:

✔ Vault uses Shamir Secret Sharing

No configuration required.

---

### AWS KMS Auto Unseal Example

    seal "awskms" {
      region     = "us-east-1"
      kms_key_id = "arn:aws:kms:xxxx"
    }

---

### Transit Auto Unseal Example

    seal "transit" {
      address    = "http://transit-vault:8200"
      token      = "s.xxxx"
      key_name   = "unseal-key"
      mount_path = "transit/"
    }

---

## Why Seal Configuration Matters

Seal choice affects:

✔ Startup behavior  
✔ Dependencies  
✔ Security model  
✔ Failure scenarios  

---

## High-Level Global Parameters

Defined outside stanzas.

Example:

    api_addr     = "https://vault.example.com:8200"
    cluster_name = "vault_cluster"
    ui           = true
    log_level    = "info"

---

### api_addr

Defines:

✔ Address Vault advertises to clients

Important for:

✔ Load balancers  
✔ HA clusters  

---

### cluster_name

Defines:

✔ Logical cluster identifier

Used for:

✔ Monitoring  
✔ Diagnostics  

---

### ui = true

Enables:

✔ Vault Web UI

---

### log_level

Controls:

✔ Logging verbosity

Options include:

✔ trace  
✔ debug  
✔ info  
✔ warn  
✔ error  

---

## What Is NOT Configured Here?

Very common beginner confusion.

Configuration File = Server Behavior

NOT

Vault Functional Setup

---

## These Are NOT Defined Here:

✔ Secrets Engines  
✔ Auth Methods  
✔ Policies  
✔ Audit Devices  
✔ Entities & Groups  

Why?

Because these are **runtime operations**.

Configured using:

✔ CLI  
✔ API  
✔ UI  

---

## Configuration File Syntax

Vault uses:

Stanzas + Parameters

---

### Basic Stanza Structure

    stanza_name "label" {
      parameter = "value"
    }

---

### Example Storage Stanza

    storage "raft" {
      path    = "/opt/vault/data"
      node_id = "node1"
    }

---

### Example Listener Stanza

    listener "tcp" {
      address = "0.0.0.0:8200"
    }

---

## Minimal Vault Configuration (Learning)

    storage "file" {
      path = "/opt/vault/data"
    }

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

    ui = true

---

## Production-Style Vault Configuration

    storage "raft" {
      path    = "/opt/vault/data"
      node_id = "node1"
    }

    listener "tcp" {
      address         = "0.0.0.0:8200"
      cluster_address = "0.0.0.0:8201"

      tls_cert_file   = "/etc/vault/tls/server.crt"
      tls_key_file    = "/etc/vault/tls/server.key"
    }

    api_addr     = "https://vault.example.com:8200"
    cluster_name = "vault_cluster"
    log_level    = "info"
    ui           = true

---

## Common Vault Stanzas

Vault provides several stanzas.

---

### storage

Defines persistence backend.

---

### listener

Defines network communication.

---

### seal

Defines master key protection mechanism.

---

### telemetry

Defines monitoring & metrics configuration.

Example:

    telemetry {
      prometheus_retention_time = "30s"
      disable_hostname = true
    }

---

## Final Mental Model — Vault Configuration File

Configuration File Defines:

✔ How Vault runs  
✔ How Vault stores data  
✔ How Vault communicates  
✔ How Vault unseals  
✔ How Vault clusters  

Configuration File Does NOT Define:

✔ Secrets  
✔ Access control  
✔ Vault usage policies  

---

## Critical Production Insights

✔ TLS is mandatory  
✔ Storage backend choice is critical  
✔ Seal type affects architecture  
✔ api_addr is crucial in HA  
✔ File permissions must be strict  

Vault config file should NEVER be world-readable.

---

## Summary

You now understand:

✔ What Vault configuration file is  
✔ Why it is required  
✔ What belongs inside it  
✔ What does NOT belong inside it  
✔ Syntax structure  
✔ Core stanzas  
✔ Production considerations  
