## Manually Installing Vault 

## Prerequisite Mental Model — What Are We Installing?

Vault is distributed as:

✔ A single executable binary

Meaning:

• No heavy installers  
• No runtime dependencies  
• Very portable  

Installation is essentially:

Download → Extract → Place in PATH

---

## Step 1 — Download Vault Binary

Run:

    curl --silent -Lo /tmp/vault.zip https://releases.hashicorp.com/vault/1.7.1_linux_amd64.zip

---

### What This Command Does

✔ Downloads Vault archive  
✔ Saves it as `/tmp/vault.zip`

Breakdown:

curl → Download tool  
--silent → Suppress progress output  
-Lo → Output to file  
/tmp/vault.zip → Save location  

---

## Why Download to /tmp?

✔ Temporary working directory  
✔ Safe staging area  
✔ Standard Linux practice  

---

## Step 2 — Move to Download Directory

    cd /tmp

Simple navigation step.

---

## Step 3 — Extract Vault Binary

    unzip vault.zip

---

### What Happens Here

✔ Archive extracted  
✔ `vault` binary created  

Vault is now present as an executable file.

---

## Step 4 — Move Binary to System Path

    sudo mv vault /usr/local/bin

---

### Why /usr/local/bin?

✔ Standard location for executables  
✔ Included in system PATH  
✔ Allows running Vault globally  

Without this step, you'd need:

    ./vault

Instead of:

    vault

---

## Step 5 — Verify Vault Installation

    vault version

---

### Expected Output

Displays Vault version information.

Confirms:

✔ Binary works  
✔ PATH configured correctly  

---

## Important Clarification — Vault Installed ≠ Vault Running

At this stage:

✔ Vault CLI available  
✔ Vault server NOT running  

Vault is just a tool until server starts.

---

## Step 6 — Start Vault Dev Server

    vault server -dev

---

## What Dev Mode Actually Does

Dev mode is a **special learning/testing mode**.

Vault automatically:

✔ Uses in-memory storage  
✔ Initializes itself  
✔ Unseals itself  
✔ Generates root token  
✔ Enables basic secrets engine  

This removes complexity for beginners.

---

## Example Dev Mode Output

You will see:

• Root Token  
• Unseal Key (informational)  
• Listening Address  

Example:

Root Token: s.xxxxx

---

## Critical Dev Mode Characteristics

✔ No manual init required  
✔ No manual unseal required  
✔ Data lost on restart  

Dev mode is perfect for:

✔ Learning  
✔ Experiments  
✔ Testing commands  

Never for production.

---

## Step 7 — Set Vault Address (New Terminal)

Open another terminal:

    export VAULT_ADDR='http://127.0.0.1:8200'

---

### Why This Step Is Needed

Vault CLI must know:

✔ Where Vault server is running  

---

## Step 8 — Login Using Root Token

    vault login <root-token>

Example:

    vault login s.xxxxx

---

## What Happens After Login

✔ Token validated  
✔ Full access granted  
✔ Vault ready for operations  

---

## Step 9 — Verify Vault Status

    vault status

Expected:

Initialized → true  
Sealed → false  

---

## Final Mental Model — Manual Installation Flow

Manual Installation:

Download → Extract → Move Binary → Verify

Running Vault:

Start Server → Set VAULT_ADDR → Login

---

## Common Beginner Confusions

---

### “Why No Configuration File?”

Dev mode bypasses configuration complexity.

Production Vault ALWAYS needs config file.

---

### “Why No Unseal Keys?”

Dev mode auto-unseals Vault.

---

### “Why Data Disappears?”

Dev mode uses:

✔ In-memory storage

No persistence.

---

## Summary

You now understand:

✔ How manual Vault installation works  
✔ What the binary actually is  
✔ Why PATH placement matters  
✔ Difference between installation & execution  
✔ What dev mode does internally  
✔ How to start Vault quickly  
✔ Why dev mode exists  
