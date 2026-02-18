## Unsealing with Transit Auto Unseal

## Prerequisite Mental Model — What Is Transit Auto Unseal?

In Transit Auto Unseal:

Primary Vault → Does NOT manage its own master key protection

Instead:

Primary Vault → Uses another Vault cluster’s Transit Engine → To encrypt/decrypt master key

Meaning:

• Another Vault cluster becomes your cryptographic protector  
• No Shamir unseal keys required  
• Primary cluster auto-unseals  

Important production rule:

The Transit cluster MUST be highly available.

If Transit cluster is down → Primary cluster cannot unseal.

---

## Lab Setup Overview

You need:

• Vault Cluster A → Transit Cluster  
• Vault Cluster B → Primary Cluster  

Both clusters:

• Vault installed  
• Vault running  
• Accessible via network  

For learning/demo:

TLS can be disabled (not recommended for production).

---

## Part 1 — Configure the Transit Cluster

SSH into **Transit Vault Node**.

---

## Step 1 — Set Vault Address

    export VAULT_ADDR='http://127.0.0.1:8200'

---

## Step 2 — Check Vault Status

    vault status

If not initialized:

Proceed.

---

## Step 3 — Initialize Transit Vault

    vault operator init

Save:

• Root Token  
• Unseal Keys  

---

## Step 4 — Unseal Transit Vault

    vault operator unseal

Provide required keys.

Repeat until unsealed.

---

## Step 5 — Login

    vault login <root-token>

---

## Step 6 — Enable Transit Secrets Engine

    vault secrets enable transit

---

## What This Does

Enables:

Transit Engine → Responsible for encryption operations

Vault now acts like:

Encryption as a Service

---

## Step 7 — Create Unseal Encryption Key

    vault write -f transit/keys/unseal-key

---

## What This Key Will Do

This key will:

Encrypt → Primary Vault Master Key  
Decrypt → Primary Vault Master Key  

---

## Step 8 — Verify Key Creation

    vault list transit/keys

You should see:

unseal-key

---

## Part 2 — Create Policy for Primary Cluster Access

Primary cluster needs permission to use Transit key.

---

## Step 9 — Create Policy File

Create:

policy.hcl

Contents:

    path "transit/encrypt/unseal-key" {
      capabilities = ["update"]
    }

    path "transit/decrypt/unseal-key" {
      capabilities = ["update"]
    }

---

## Why These Permissions?

Primary Vault must:

• Encrypt master key  
• Decrypt master key  

Nothing else.

Least privilege security.

---

## Step 10 — Write Policy

    vault policy write unseal policy.hcl

---

## Step 11 — Verify Policy

    vault policy list

---

## Step 12 — Create Token with Policy

    vault token create -policy=unseal

Save:

Generated Token

This token will be used by Primary Vault.

---

## Part 3 — Configure Primary Vault Cluster

SSH into **Primary Vault Node**.

---

## Step 1 — Stop Vault (If Running)

    sudo systemctl stop vault

---

## Step 2 — Edit Vault Configuration

Open:

    sudo vi /etc/vault.d/vault.hcl

---

## Step 3 — Add Transit Seal Stanza

    seal "transit" {
      address = "http://<TRANSIT_VAULT_IP>:8200"

      token = "<TOKEN_FROM_TRANSIT_CLUSTER>"

      key_name = "unseal-key"
      mount_path = "transit/"
    }

---

## Critical Fields Explained

### address

Where Transit Vault is running.

---

### token

Token created with encrypt/decrypt permissions.

---

### key_name

Transit encryption key name.

---

### mount_path

Transit engine mount path.

Default:

transit/

---

## Step 4 — Save Configuration

Exit editor.

---

## Step 5 — Start Vault

    sudo systemctl start vault

---

## Part 4 — Verify Primary Vault Status

---

## Step 6 — Set Vault Address

    export VAULT_ADDR='http://127.0.0.1:8200'

---

## Step 7 — Check Status

    vault status

Expected:

Seal Type → transit  
Initialized → false  
Sealed → true  

---

## What This Means

Vault now:

• Uses Transit seal mechanism  
• Still needs initialization  
• Will auto-unseal after init  

---

## Part 5 — Initialize Primary Vault

---

## Step 8 — Initialize Vault

    vault operator init

---

## Observe Output Carefully

You will receive:

• Recovery Keys  
• Root Token  

Important:

NO Unseal Keys

Because:

Auto Unseal is active.

---

## What Happens Internally

Vault:

1. Generates master key
2. Sends master key to Transit Vault
3. Transit Vault encrypts master key
4. Encrypted master key stored
5. Vault retrieves decrypted key automatically
6. Vault becomes unsealed

---

## Part 6 — Verify Vault State

---

## Step 9 — Check Status

    vault status

Expected:

Initialized → true  
Sealed → false  
Seal Type → transit  

---

# Part 7 — Login

---

## Step 10 — Login

    vault login <root-token>

---

## Final Mental Model — Transit Auto Unseal Workflow

Primary Vault Startup:

Vault → Reads Encrypted Master Key  
Vault → Calls Transit Cluster  
Transit Cluster → Decrypts Master Key  
Vault → Decrypts Encryption Key  
Vault → Operational  

---

## Critical Production Considerations

---

## Transit Cluster MUST Be Highly Available

If Transit Vault is unavailable:

• Primary Vault cannot unseal  
• Vault restart may fail  

---

## Token Security Is Critical

Token grants:

• Encrypt / Decrypt capability

Compromised token → Compromised Vault security.

---

## Key Rotation Supported

Transit key can be rotated without downtime.

Major security advantage.

---

## Common Beginner Confusions

---

## “Why Do I Still Get Keys?”

With Auto Unseal:

• Recovery Keys → Emergency operations  
• NOT used for unsealing  

---

## “Is Transit Vault a Single Point of Failure?”

Yes — unless highly available.

Hence HA requirement.

---

## Summary

You configured:

• Transit Vault → Encryption Provider  
• Primary Vault → Auto-Unsealing Client  

Vault now uses:

Vault → Vault trust model
