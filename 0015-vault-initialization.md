## Vault Initialization Parameters and Enabling KV v2

## Part 1 — Customizing Vault Initialization

## Prerequisite Mental Model — What Are We Customizing?

During initialization, Vault:

• Generates a Master Key  
• Splits the Master Key using Shamir Secret Sharing  

By default:

• Key Shares = 5  
• Key Threshold = 3  

Vault allows us to change this behavior.

---

## Command for Custom Initialization

    vault operator init -key-shares=3 -key-threshold=2

---

## Understanding the Parameters

### key-shares=3

Vault generates:

• 3 Unseal Keys (Key Shards)

---

### key-threshold=2

Vault requires:

• Any 2 keys → To Unseal Vault

---

## Why Customize Shares and Threshold?

This controls your **security trust model**.

Examples:

Small Team:

• Fewer keys → Easier operations  

High Security:

• More keys → Stronger distributed trust  

---

## Step-by-Step Hands-On Workflow

---

## Step 1 — Check Vault Status

    vault status

If Vault is fresh:

Initialized → false  
Sealed → true  

---

## Step 2 — Initialize Vault with Custom Parameters

    vault operator init -key-shares=3 -key-threshold=2

---

## Example Output

    Unseal Key 1: XXXX
    Unseal Key 2: XXXX
    Unseal Key 3: XXXX

    Initial Root Token: s.xxxx

---

## Important Observations

✔ Only 3 keys generated  
✔ Only 2 required for unseal  

Vault is now:

Initialized → true  
Sealed → true  

---

## Step 3 — Verify Vault Status

    vault status

Expected:

Initialized → true  
Sealed → true  
Total Shares → 3  
Threshold → 2  

---

## Step 4 — Unseal Vault

    vault operator unseal

Provide:

Unseal Key 1

Vault Output:

Unseal Progress → 1/2  

---

## Step 5 — Provide Second Key

    vault operator unseal

Provide:

Unseal Key 2

Vault becomes:

Sealed → false  

---

## Step 6 — Login

    vault login <root-token>

Vault is now operational.

---

## Part 2 — Enabling KV v2 Secrets Engine at Custom Path

---

## Prerequisite Mental Model — What Is KV v2?

KV = Key-Value Secrets Engine

KV v2 = Versioned Key-Value Engine

Features:

• Versioned secrets  
• Metadata tracking  
• Soft deletes  
• Rollbacks  

---

## Command to Enable KV v2

    vault secrets enable -path=secrets kv-v2

---

## Breaking Down the Command

### kv-v2

Type of secrets engine:

Key-Value Version 2

---

### -path=secrets

Mount path:

Instead of default:

secret/

We create:

secrets/

---

## Why Use Custom Paths?

Custom paths help with:

• Organization  
• Environment separation  
• Naming conventions  

Example:

secrets/dev/  
secrets/prod/  

---

## Step-by-Step Hands-On Workflow

---

## Step 1 — Enable Secrets Engine

    vault secrets enable -path=secrets kv-v2

Expected Output:

Success! Enabled the kv-v2 secrets engine at: secrets/

---

## Step 2 — Verify Mounted Engines

    vault secrets list

Example Output:

    Path         Type
    ----         ----
    secrets/     kv
    sys/         system
    cubbyhole/   cubbyhole
    identity/    identity

---

## What Happened Internally?

Vault:

✔ Mounted KV Engine  
✔ Attached it to secrets/ path  
✔ Created isolated routing namespace  

---

## Step 3 — Write a Secret

    vault kv put secrets/myapp/config username=admin password=1234

Vault:

✔ Encrypts data  
✔ Stores Version 1  

---

## Step 4 — Read Secret

    vault kv get secrets/myapp/config

---

## Step 5 — Update Secret (Create New Version)

    vault kv put secrets/myapp/config password=5678

Vault now stores:

✔ Version 1 → Old value  
✔ Version 2 → New value  

---

## Step 6 — Read Specific Version

    vault kv get -version=1 secrets/myapp/config

---

## Critical KV v2 Insight

KV v2 is NOT simple storage.

It is:

✔ Versioned secret lifecycle system  

---

## Final Mental Model

Initialization Parameters:

Define security & trust model

Secrets Engine Mounting:

Define Vault structure

Paths:

Define routing + isolation + access control

---

## Summary

You learned:

✔ How to initialize Vault with custom shares/threshold  
✔ How Shamir parameters affect unseal process  
✔ How to enable KV v2 engine  
✔ Why custom paths matter  
✔ Basic KV operations  

