## How Does Vault Protect My Data?

## Prerequisite Mental Model — Two Layers of Protection

Vault uses **layered encryption**.

Think of it like a safe inside another safe.

Vault protects:

1. Your Data
2. The Key That Encrypts Your Data

This separation is the foundation of Vault’s security design.

---

## Step 1 — Vault Encrypts Your Data

## What Happens When Vault Stores Data?

When Vault writes secrets to the storage backend:

Vault does NOT store plaintext.

Instead:

✔ Vault encrypts the data first  
✔ Then writes encrypted data to storage  

---

## Which Key Encrypts the Data?

Vault uses:

**Encryption Key (also called Data Encryption Key / DEK)**

This key is responsible for:

✔ Encrypting secrets  
✔ Decrypting secrets  

Important:

Your actual secrets are encrypted using this key.

---

## Step 2 — Protecting the Encryption Key

Now comes the critical security question:

If someone steals the encryption key → They can decrypt everything.

So Vault must protect:

**The Encryption Key Itself**

---

## Vault’s Solution → Master Key

Vault introduces another key:

**Master Key**

Role of Master Key:

✔ Encrypts the Encryption Key  
✔ Never directly encrypts secrets  

This creates **encryption layering**:

Data → Encrypted by Encryption Key  
Encryption Key → Encrypted by Master Key

---

## Why This Design Is Brilliant

This layered model provides:

✔ Stronger security isolation  
✔ Key rotation flexibility  
✔ Protection against storage compromise  

Even if storage is stolen:

Attacker sees:

✔ Encrypted data  
✔ Encrypted encryption key  

Without master key → Nothing is readable.

---

# Step 3 — Where Keys Live

Understanding where keys are stored is crucial.

---

## Encryption Key (Data Key)

### 1. Stored on Storage Backend

The encryption key is stored:

✔ Alongside encrypted data  
✔ Inside a keyring  

But remember:

✔ It is itself encrypted by the master key

So storage contains:

Encrypted Data  
Encrypted Encryption Key

---

### 2. Why Store Encryption Key in Storage?

Vault nodes must:

✔ Restart safely  
✔ Recover state  
✔ Support HA  

Thus encryption key must persist.

---

## Master Key

This is where Vault becomes very interesting.

Vault treats the master key differently depending on **unseal mechanism**.

---

## Traditional Unseal (Default Model)

## 1. Master Key Is NEVER Stored in Storage

Critical rule:

✔ Master key is NOT written to storage backend

Why?

Because storing master key with encrypted data defeats security.

---

## 2. Master Key Is Split into Unseal Keys

Vault uses:

**Shamir Secret Sharing**

Vault:

✔ Splits master key into multiple parts  
✔ Distributes parts as Unseal Keys  

Example:

Master Key → Split into 5 shares  
Require 3 shares to reconstruct

Security benefits:

✔ No single point of compromise  
✔ Human-controlled trust model  

---

## 3. Vault Starts in Sealed State

When Vault starts:

✔ Encryption key is locked  
✔ Data cannot be decrypted  

Vault is **sealed**.

Only after providing enough unseal keys:

✔ Master key reconstructed  
✔ Encryption key decrypted  
✔ Vault becomes operational

---

## Auto Unseal (Modern Cloud-Native Model)

## 1. Master Key IS Stored — But Encrypted

With auto unseal:

✔ Master key is encrypted  
✔ Stored securely in storage backend  

But protected by:

✔ Cloud KMS  
✔ HSM  

Examples:

- AWS KMS
- Azure Key Vault
- GCP KMS
- Hardware Security Module (HSM)

---

## 2. Vault Retrieves Master Key Automatically

On startup:

Vault:

✔ Contacts KMS / HSM  
✔ Retrieves master key  
✔ Decrypts encryption key  
✔ Unseals automatically  

No manual unseal keys required.

---

## Why Auto Unseal Exists

Auto unseal solves:

✔ Operational friction  
✔ HA complexity  
✔ Cloud automation needs  

Especially useful for:

✔ Cloud deployments  
✔ Kubernetes  
✔ Large-scale systems  

---

## Master Key — Detailed Properties

### 1. Creation

Master key is created during:

✔ Vault Initialization  
✔ Rekey Operation  

Vault generates it internally.

---

### 2. Storage Behavior

Depends on unseal mode:

Traditional Unseal → Never stored  
Auto Unseal → Stored encrypted

---

### 3. Security Role

✔ Encrypts encryption key  
✔ Root of Vault’s cryptographic barrier  

Without master key → Vault unusable

---

## Encryption Key — Detailed Properties

### 1. Storage

✔ Stored in keyring on backend  
✔ Always encrypted  

---

### 2. Rotation

Encryption key can be:

✔ Rotated easily  
✔ Without data loss  

Vault supports:

✔ Key rolling  
✔ Online rotation  

Benefits:

✔ Limits blast radius  
✔ Improves cryptographic hygiene  

---

### 3. Why Rotation Matters

If encryption key is compromised:

✔ Only limited data exposure  
✔ New key protects future data  

Vault encourages frequent rotation.

---

## Full Encryption Flow (Mental Model)

Vault encryption hierarchy:

Master Key  
    ↓ encrypts  
Encryption Key  
    ↓ encrypts  
Your Secrets

---

## What Happens During Vault Startup

Sealed Vault:

✔ Encryption key locked  
✔ Data unreadable  

Unseal Process:

✔ Master key obtained  
✔ Encryption key decrypted into memory  
✔ Data accessible  

Important:

✔ Encryption key lives ONLY in memory during runtime

Never exposed externally.

---

## Final Summary — How Vault Protects Data

Vault protects data using:

✔ Strong AES-256 encryption  
✔ Layered key hierarchy  
✔ Master key isolation  
✔ Shamir secret sharing  
✔ Auto-unseal with KMS/HSM  
✔ Memory-only decrypted keys  

This provides:

✔ Protection against storage theft  
✔ Protection against insider threats  
✔ Strong cryptographic guarantees  


