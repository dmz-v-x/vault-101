## How to Run Vault Dev Server

## Prerequisite Mental Model — What Is Dev Server?

Dev Server is a **special Vault startup mode**.

When you run:

    vault server -dev

Vault automatically handles:

✔ Storage  
✔ Initialization  
✔ Unsealing  
✔ Root authentication  
✔ UI enablement  

No configuration file required.

---

## Why Dev Server Exists

Running Vault in production requires:

• Storage backend  
• TLS certificates  
• Seal configuration  
• Initialization  
• Unsealing  

For beginners, this is overwhelming.

Dev mode eliminates those steps.

---

## What Happens Internally When Dev Server Starts

This is the most important part to understand.

Vault Dev Server automatically:

✔ Uses in-memory storage  
✔ Generates master key  
✔ Initializes Vault  
✔ Unseals Vault  
✔ Enables UI  
✔ Mounts KV v2 engine  
✔ Generates root token  

Everything is done for you.

---

## Step 1 — Start Dev Server

Open terminal and run:

    vault server -dev

---

## What You Will See

Vault prints startup details.

Example output contains:

• API Address  
• Listener Address  
• Root Token  
• Unseal Key (informational)

Example:

Root Token: s.xxxxx

---

## Critical Dev Server Behavior

Vault is now:

✔ Initialized → true  
✔ Sealed → false  

Immediately usable.

---

## Step 2 — Understand Listener Configuration

Dev server automatically sets:

    127.0.0.1:8200

Meaning:

✔ Vault listens ONLY on localhost  
✔ No remote connections allowed  

This improves safety during learning.

---

## Why Localhost Only?

Dev server is:

✔ Insecure  
✔ No TLS  
✔ Clear-text communication  

Restricting to localhost reduces exposure.

---

## Step 3 — Dev Server Uses In-Memory Storage

Very important concept.

Dev mode storage:

✔ Runs entirely in RAM  

Meaning:

✔ Extremely fast  
✔ Completely non-persistent  

---

## What Happens When You Stop Vault?

Close terminal → Vault stops → Data wiped

Everything disappears:

• Secrets  
• Tokens  
• Policies  
• Configurations  

---

## Step 4 — Vault Automatically Initializes Itself

Normally, you must run:

    vault operator init

Dev server skips this.

Vault internally:

✔ Generates master key  
✔ Generates encryption key  

No user involvement required.

---

## Step 5 — Vault Automatically Unseals Itself

Normally, you must run:

    vault operator unseal

Dev server:

✔ Unseals automatically  

Encryption key loaded into memory.

---

## Step 6 — Root Token Generation

Dev server prints:

✔ Root Token

Example:

Root Token: s.xxxxx

This token grants:

✔ Full administrative access

---

## Step 7 — Set Vault Address (New Terminal)

Open another terminal.

Set environment variable:

    export VAULT_ADDR='http://127.0.0.1:8200'

---

## Why This Step Matters

Vault CLI must know:

✔ Where Vault server is running

Without this → CLI cannot connect.

---

## Step 8 — Login to Vault

    vault login <root-token>

Example:

    vault login s.xxxxx

---

## What Happens After Login

✔ Token validated  
✔ Identity attached  
✔ Full access granted  

Vault ready for operations.

---

## Step 9 — KV v2 Secrets Engine Auto-Mounted

Dev server automatically enables:

✔ KV Version 2

Mounted at:

    secret/

---

## Why This Is Important

You can immediately:

✔ Store secrets  
✔ Read secrets  

Without manual engine setup.

---

## Step 10 — Store a Secret

    vault kv put secret/myapp/config username=admin password=1234

Vault:

✔ Encrypts secret  
✔ Stores in memory  

---

## Step 11 — Read Secret

    vault kv get secret/myapp/config

---

## Dev Server Security Characteristics (Critical)

Dev server is intentionally insecure.

✔ No TLS  
✔ Clear-text traffic  
✔ In-memory storage  
✔ Root token exposed in console  

Designed ONLY for learning/testing.

---

## Why Dev Server Does NOT Use TLS

TLS setup adds complexity.

Dev mode prioritizes:

✔ Speed  
✔ Simplicity  

---

## Critical Warning — Never Use Dev Server in Production

Dev mode lacks:

✔ TLS encryption  
✔ Persistent storage  
✔ Proper security guarantees  

Using dev mode in production = Severe security risk.

---

## Where Dev Server Should Be Used

✔ Proof of Concepts  
✔ Feature testing  
✔ Development environments  
✔ Integration experiments  
✔ Learning Vault  

---

## Where Dev Server Must NEVER Be Used

✔ Production systems  
✔ Real secrets  
✔ Internet-exposed machines  

---

## Final Mental Model — Vault Dev Server

Vault Dev Server = Instant Vault Playground

Vault:

✔ Self-configures  
✔ Self-initializes  
✔ Self-unseals  
✔ Self-authenticates  

Tradeoff:

✔ Convenience over security

---

## Summary

You now understand:

✔ What dev server is  
✔ Why it exists  
✔ What Vault auto-configures  
✔ Why no config file required  
✔ In-memory storage behavior  
✔ Listener restrictions  
✔ Root token generation  
✔ KV engine auto-mount  
✔ Security limitations  
