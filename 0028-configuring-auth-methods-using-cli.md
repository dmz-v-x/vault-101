## Configuring Auth Methods Using CLI 

## Prerequisite Mental Model — CLI = API Wrapper

Vault CLI commands are simply:

✔ Human-friendly wrappers around HTTP API calls

When you run:

    vault auth enable userpass

Vault CLI internally invokes:

✔ /sys/auth API

Understanding this helps remove confusion.

---

## Core Command — `vault auth`

Vault provides a dedicated command group:

    vault auth

Used for managing authentication methods.

---

## Available Subcommands

Vault auth supports:

✔ enable  
✔ disable  
✔ list  
✔ tune  
✔ help  

Each performs a different operation.

---

## Step 1 — Get Help (Always First Habit)

    vault auth -h

Why this matters:

✔ Shows valid syntax  
✔ Displays available options  
✔ Prevents mistakes  

Professional operators rely heavily on help flags.

---

## Step 2 — Enable an Auth Method (Default Path)

    vault auth enable approle

---

### What Happens Internally

Vault:

✔ Loads AppRole auth plugin  
✔ Mounts at default path → approle/  
✔ Registers API routes  

---

## Default Path Rule (Very Important)

If no path specified:

✔ Path = Auth Method Type

Examples:

✔ approle → approle/  
✔ userpass → userpass/  
✔ aws → aws/

---

## Step 3 — List Enabled Auth Methods

    vault auth list

---

### Example Output

    Path         Type        Accessor
    ----         ----        --------
    token/       token       auth_token_xxxx
    approle/     approle     auth_approle_xxxx

---

### What This Output Means

✔ Path → Mount location  
✔ Type → Auth mechanism  
✔ Accessor → Internal identifier  

---

## Step 4 — Enable Auth Method at Custom Path

    vault auth enable -path=vault-course userpass

Vault response:

Success! Enabled userpass auth method at: vault-course/

---

## Why Custom Paths Matter

Custom paths allow:

✔ Logical separation  
✔ Multiple instances  
✔ Environment isolation  

Example:

✔ userpass-dev/  
✔ userpass-prod/

---

## Generic Custom Path Syntax

    vault auth enable -path=<custom-name> <auth-type>

Example:

    vault auth enable -path=apps approle

---

## Step 5 — Understanding CLI Command Structure

Vault CLI follows predictable grammar.

---

## Basic Pattern

    vault <object> <action> <type>

Example:

    vault auth enable approle

Breakdown:

vault → CLI tool  
auth → Vault object category  
enable → Subcommand  
approle → Auth method type  

---

## Pattern with Custom Path

    vault auth enable -path=apps approle

Meaning:

Enable AppRole → Mount at apps/

---

## Pattern with Description

    vault auth enable -path=apps -description="My Apps Auth" approle

Descriptions help with:

✔ Documentation  
✔ Operational clarity  
✔ UI readability  

---

## Critical Rule — Description Cannot Be Added Later

Once enabled:

✔ Path immutable  
✔ Description immutable  

To change:

✔ Disable → Re-enable

---

## Step 6 — Disable an Auth Method

    vault auth disable userpass

Important nuance:

"userpass" here refers to:

✔ Path name  
✔ NOT auth type

---

## Critical Clarification — Disable Uses PATH

Command:

    vault auth disable <mount-path>

Example:

    vault auth disable apps

---

## Why This Distinction Matters

You may have:

✔ Multiple userpass auth methods  

Vault must know which mount to remove.

---

## Step 7 — Verify After Disabling

    vault auth list

Observe updated mount list.

---

## Step 8 — Re-enable with Proper Description

    vault auth enable -path=bryan -description="Local credentials for Vault" userpass

---

## Step 9 — Modify Auth Method Configuration (Tune)

Vault allows runtime tuning.

Command:

    vault auth tune

---

## Example Tune Operation

    vault auth tune -default-lease-ttl=24h bryan/

---

### What This Does

✔ Changes default TTL for tokens  
✔ Affects future tokens  
✔ Does NOT modify existing tokens  

---

## Why Tune Matters

Allows:

✔ Policy adjustments  
✔ TTL tuning  
✔ Operational refinements  

Without disabling auth method.

---

## Step 10 — Configuring Auth Method Using `auth/` Prefix

Once enabled:

✔ Interact via auth path

General syntax:

    vault write auth/<path>/<options>

---

## Example — Configure userpass User

    vault write auth/bryan/users/krausen \
        password=vault \
        policies=bryan

---

### What Happens Here

Vault:

✔ Creates user → krausen  
✔ Sets password  
✔ Attaches policies  

---

## Step 11 — List Configured Users

    vault list auth/bryan/users

---

## Step 12 — Read User Configuration

    vault read auth/bryan/users/krausen

---

### Why Read Matters

✔ Validate configuration  
✔ Debug issues  
✔ Confirm policy assignment  

---

## Step 13 — Configuring AppRole Roles

Example:

    vault write auth/approle/role/bryan \
        token_ttl=20m \
        policies=bryan

---

### What Happens Here

Vault:

✔ Creates AppRole → bryan  
✔ Defines token TTL  
✔ Attaches policies  

---

## Advanced Example — Detailed Role Configuration

    vault write auth/approle/role/vault-course \
        secret_id_ttl=10m \
        token_num_uses=10 \
        token_ttl=20m \
        token_max_ttl=30m \
        secret_id_num_uses=40

---

### Parameters Explained

✔ secret_id_ttl → Lifetime of SecretID  
✔ token_ttl → Token lifetime  
✔ token_max_ttl → Maximum renewal window  
✔ token_num_uses → Usage restrictions  
✔ secret_id_num_uses → SecretID limits  

This controls:

✔ Security posture  
✔ Credential lifecycle  
✔ Risk exposure  

---

## Final Mental Model — CLI Auth Management

Auth Method Lifecycle:

Enable → Configure → Tune → Use → Disable (if needed)

Everything revolves around:

✔ Mount Paths  
✔ Tokens  
✔ Policies  

---

## Critical Production Insights

✔ Plan mount paths carefully  
✔ Use descriptions for clarity  
✔ Avoid frequent disable/re-enable  
✔ Use tune for adjustments  
✔ Always verify with list/read  
✔ Restrict sys/auth permissions  

---

## Summary

You now understand:

✔ vault auth command structure  
✔ Enabling auth methods  
✔ Default vs custom paths  
✔ Description usage  
✔ Disabling auth methods  
✔ Tune operation  
✔ Configuring userpass  
✔ Configuring approle  
✔ Reading & listing configurations  
