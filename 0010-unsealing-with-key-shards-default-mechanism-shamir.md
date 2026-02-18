## Unsealing with Key Shards

## Step 1 — SSH into the Vault Node

Example scenario:

You open an SSH session to a machine running Vault in AWS.

At this moment:

- Vault process is running
- Vault is most likely sealed
- Vault may or may not be initialized

Important:

Running Vault ≠ Initialized Vault

---

## Step 2 — Check Vault Status

Command:

vault status

Example output for a fresh Vault:

Seal Type       shamir  
Initialized     false  
Sealed          true  

---

## Understanding the Output

### Seal Type = shamir

Vault is using the default unseal mechanism:

Shamir Secret Sharing

This is Vault’s traditional sealing model.

No special configuration required.

---

### Initialized = false

This is extremely important.

Initialized = false means:

- Vault has NOT generated its master key
- Vault has NOT generated encryption keys
- Vault is not ready for use

Vault is essentially an empty cryptographic container.

---

### Sealed = true

Vault is cryptographically locked.

Even though Vault knows:

- Where storage is
- How to access storage

It cannot decrypt anything.

---

## Step 3 — Inspect Vault Configuration File

Command:

cat /etc/vault.d/vault.hcl

Observation:

You will NOT see anything related to sealing/unsealing.

---

## Why No Seal Configuration Exists

Because:

Shamir is the default seal mechanism.

Vault automatically handles:

- Master key generation
- Key shard splitting
- Unseal logic

You only configure a seal stanza when using:

- Auto Unseal (AWS KMS / Azure / GCP / HSM / Transit)

---

## Step 4 — Initialize Vault

Command:

vault operator init

This is one of the most critical Vault commands.

---

## What Happens During Initialization

Vault performs several internal operations:

### 1. Generates Master Key

Vault creates the root cryptographic secret.

This key protects the encryption key.

---

### 2. Splits Master Key into Shards

Using Shamir Secret Sharing:

Default behavior:

- Total Shares = 5
- Threshold = 3

Meaning:

Any 3 shards → Reconstruct master key

---

### 3. Generates Encryption Key

Vault creates the key used to encrypt secrets.

---

### 4. Generates Initial Root Token

Vault issues:

Initial Root Token

This token has full administrative access.

---

## Example Initialization Output

You will see something like:

Unseal Key 1: XXXXX  
Unseal Key 2: XXXXX  
Unseal Key 3: XXXXX  
Unseal Key 4: XXXXX  
Unseal Key 5: XXXXX  

Initial Root Token: s.XXXXXX

---

## Critical Security Responsibility

At this point:

YOU are responsible for:

- Storing unseal keys securely
- Protecting root token
- Preventing leaks

Never:

- Store in plaintext files
- Commit to Git
- Share via chat/email

---

## Step 5 — Check Vault Status Again

Command:

vault status

Now output becomes:

Seal Type       shamir  
Initialized     true  
Sealed          true  
Total Shares    5  
Threshold       3  

---

## What Changed?

Initialized = true means:

- Vault master key exists
- Vault encryption key exists
- Vault is configured internally

BUT:

Vault is still sealed.

Encryption key is still locked.

---

## Step 6 — Unseal Vault

Command:

vault operator unseal

Vault asks for:

Unseal Key

---

## What Vault Is Doing Internally

Each unseal key:

- Reconstructs part of master key

Vault tracks progress.

---

## Example After First Key

Sealed          true  
Unseal Progress 1/3  

Meaning:

- 1 shard provided
- 2 more required

---

## Continue Unsealing

Run again:

vault operator unseal

Provide another key.

Repeat until threshold reached.

---

## Example Final State

Sealed          false  

Vault is now operational.

---

## What Happened Internally

Vault:

- Reconstructed master key
- Decrypted encryption key
- Loaded encryption key into memory

Secrets remain encrypted in storage.

---

## Step 7 — Login to Vault

Command:

vault login <root-token>

Example:

vault login s.xxxxxx

---

## What Happens During Login

Vault:

- Validates token
- Attaches root identity
- Grants administrative privileges

---

## Root Token Characteristics

Root token:

- Has full access
- Bypasses policies
- Often has no expiration (initial token)

Best practice:

Do NOT use root token for daily operations.

Create limited tokens instead.

---

## Final Mental Model

Initialization:

Creates cryptographic foundation.

Unsealing:

Unlocks encryption capability.

Login:

Grants identity & permissions.

These are three completely different phases.

---

## Common Beginner Mistakes

Mistake 1:

Thinking init = unseal

Wrong.

Init creates keys.  
Unseal unlocks Vault.

---

Mistake 2:

Losing unseal keys

Without threshold keys → Vault permanently inaccessible.

---

Mistake 3:

Using root token everywhere

Major security risk.

---

## Summary

Vault Default Workflow:

1. Vault starts sealed
2. Check status
3. Initialize Vault
4. Vault remains sealed
5. Provide unseal keys
6. Vault becomes unsealed
7. Login using token
8. Perform operations
