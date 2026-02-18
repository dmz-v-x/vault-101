## Seal and Unseal

## Prerequisite Mental Model — Why Vault Can Be “Sealed”

Remember from the previous topic:

Vault uses:

Master Key → Encrypts → Encryption Key → Encrypts → Data

When Vault starts:

✔ Vault knows where storage is  
✔ Vault can access encrypted data  
✔ BUT Vault cannot decrypt anything  

Why?

Because the **encryption key is locked**.

That locked state is called:

**Sealed State**

---

## What Does “Sealed Vault” Actually Mean?

When Vault is sealed:

✔ Vault can read encrypted data from storage  
✔ Vault CANNOT decrypt data  
✔ Vault CANNOT decrypt encryption key  
✔ Vault CANNOT serve secrets  

Vault is basically **cryptographically locked**.

---

## What Operations Are Possible When Vault Is Sealed?

Almost none.

Allowed:

✔ vault status  
✔ vault operator unseal  

Blocked:

✔ Reading secrets  
✔ Writing secrets  
✔ Authentication  
✔ Policy evaluation  
✔ Token usage  

This is intentional.

Vault prioritizes **security over availability**.

---

## What Does “Unsealing Vault” Mean?

Unsealing means:

Vault reconstructs the **master key**, which then:

✔ Decrypts the encryption key  
✔ Loads encryption key into memory  
✔ Allows Vault to operate normally  

Important:

Vault does NOT decrypt all secrets into memory.

Vault only decrypts:

✔ Encryption key

Secrets remain encrypted in storage.

---

## What Happens After Vault Is Unsealed?

Vault:

✔ Stores encryption key in memory (RAM)  
✔ Can encrypt new data  
✔ Can decrypt requested data  
✔ Becomes fully operational  

Critical security detail:

✔ Encryption key lives ONLY in memory during runtime

---

## What Does “Sealing Vault” Mean?

Sealing Vault means:

✔ Vault discards encryption key from memory  
✔ Vault becomes cryptographically locked again  
✔ Requires another unseal process  

Think of sealing as:

“Emergency cryptographic shutdown”

---

## When Does Vault Become Sealed?

Vault becomes sealed when:

✔ Vault starts (default behavior)  
✔ Vault restarts  
✔ Vault crashes  
✔ You manually seal it  

Vault **always starts sealed**.

This is a fundamental security guarantee.

---

## Why Would You Manually Seal Vault?

Manual sealing is a **security response mechanism**.

Seal Vault when:

### 1. Unseal Keys (Key Shards) Are Exposed

If unseal keys are leaked:

✔ Master key may be reconstructable  
✔ Encryption key at risk  

Seal Vault immediately.

---

### 2. Detection of Compromise or Intrusion

If Vault node is compromised:

✔ Encryption key in memory may be accessible  

Seal Vault to invalidate memory-resident keys.

---

### 3. Malware / Spyware Detection

If malicious software exists:

✔ Memory scraping becomes possible  

Seal Vault as defensive action.

---

## Seal and Unseal Options

Vault supports multiple unseal mechanisms.

---

# 1. Traditional Unseal — Shamir Secret Sharing

This is the **default Vault mechanism**.

---

## What Problem Does Shamir Solve?

We established earlier:

✔ Master key must NOT be stored  
✔ Master key must be protected  

Vault solves this using:

**Shamir’s Secret Sharing**

---

## Shamir’s Secret Sharing (Simple Explanation)

A secret (master key) is:

✔ Split into multiple pieces (shares)  
✔ Only a subset required to reconstruct  

Example:

5 shares created  
Threshold = 3  

Any 3 shares → Reconstruct master key

---

## Why This Is Secure

✔ No single person has full key  
✔ No single point of compromise  
✔ Distributed trust model  

Used heavily in:

✔ Enterprise security environments  
✔ Compliance-driven systems  

---

## Key Terms You Must Understand

### Total Shares (n)

How many shards Vault generates.

Example:

Total Shares = 5

---

### Threshold (t)

Minimum shards required.

Example:

Threshold = 3

Meaning:

Any 3 of 5 shards can unseal.

---

## Example Vault Status (Sealed)

vault status

key                 value  
---                 -----  
Seal Type           shamir  
Sealed              true  
Total Shares        5  
Threshold           3  
Unseal Progress     2/3  

Meaning:

✔ Vault sealed  
✔ 2 keys provided  
✔ Needs 1 more key  

---

## Example Vault Status (Unsealed)

key                 value  
---                 -----  
Seal Type           shamir  
Sealed              false  
Total Shares        5  
Threshold           3  
Storage Type        consul  
Cluster Name        vault-cluster  
HA Enabled          true  

Meaning:

✔ Vault operational  
✔ Encryption key in memory  

---

## How Traditional Unsealing Works (Step-by-Step)

---

### Step 1. Initialize Vault

Command:

vault operator init

What Vault generates:

✔ Unseal keys (shards)  
✔ Initial root token  

Example Output:

Unseal Key 1: XXXX  
Unseal Key 2: XXXX  
...  
Initial Root Token: s.xxxx  

---

### Step 2. Securely Store Unseal Keys

Critical best practice:

✔ Never store in plaintext  
✔ Never commit to Git  
✔ Use secure channels  

Common approaches:

✔ GPG encryption  
✔ Hardware vaults  
✔ Secret managers  

---

### Step 3. Vault Starts Sealed

After restart:

✔ Encryption key locked  
✔ Vault sealed  

---

### Step 4. Provide Unseal Keys

Command:

vault operator unseal

Vault asks for:

✔ Key shard  

Repeat until threshold reached.

---

### Step 5. Master Key Reconstructed

Vault:

✔ Combines shards  
✔ Rebuilds master key  
✔ Decrypts encryption key  
✔ Loads encryption key into memory  

Vault becomes operational.

---

# 2. Cloud Auto Unseal (Modern Approach)

Cloud auto unseal removes manual shard handling.

---

## How Cloud Auto Unseal Works

Instead of Shamir shards:

✔ Master key encrypted  
✔ Stored securely  
✔ Protected by Cloud KMS  

Examples:

✔ AWS KMS  
✔ Azure Key Vault  
✔ GCP KMS  

---

## Vault Startup with Auto Unseal

Vault:

✔ Contacts KMS  
✔ Retrieves master key  
✔ Decrypts encryption key  
✔ Unseals automatically  

No human interaction required.

---

## Why Organizations Prefer Auto Unseal

✔ Easier operations  
✔ Better for automation  
✔ Better for HA environments  
✔ Better for Kubernetes  

---

# 3. Transit Auto Unseal (Advanced Setup)

Transit auto unseal uses:

✔ Another Vault cluster

Yes — Vault can unseal Vault.

---

## How Transit Auto Unseal Works

Vault A:

✔ Stores master key  

Vault B:

✔ Uses Vault A transit engine  
✔ Retrieves key securely  

Used in:

✔ Complex enterprise architectures  
✔ Multi-cluster designs  

---

## Final Mental Model — Seal & Unseal

Vault Sealed:

✔ Encryption key locked  
✔ No secret operations  

Vault Unsealed:

✔ Encryption key in memory  
✔ Full functionality  

Sealing Vault:

✔ Discards encryption key  
✔ Immediate cryptographic lock  

Unsealing Vault:

✔ Reconstruct master key  
✔ Unlock encryption key  

---

## Why This Design Is Critical for Security

This mechanism protects against:

✔ Storage theft  
✔ Node compromise  
✔ Insider threats  
✔ Memory attacks  

Vault is secure **even when infrastructure is hostile**.
