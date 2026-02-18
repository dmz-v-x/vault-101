## Vault Entities 

## Prerequisite Mental Model — Identity vs Authentication

Before entities make sense, we must separate two ideas:

✔ Authentication → How you prove identity  
✔ Identity → Who Vault believes you are  

Most beginners accidentally mix these.

---

## The Problem Vault Must Solve

Imagine a single human user:

Bryan

Bryan logs in using:

✔ LDAP  
✔ userpass  
✔ OIDC  
✔ GitHub  

Question:

How does Vault know these logins belong to **the same Bryan**?

Without identity unification:

✔ Vault would treat each login separately  
✔ Policies become fragmented  
✔ Authorization becomes messy  

Entities solve this.

---

## What Is a Vault Entity?

An **Entity** is:

Vault’s internal representation of a single person or system.

Think of entity as:

Vault’s Master Identity Record

Each entity has:

✔ Unique entity_id  
✔ Policies  
✔ Metadata  
✔ Aliases  

---

## What Is an Alias?

An **Alias** is:

A mapping between:

Auth Method + External Identity → Vault Entity

Alias connects:

“How you logged in” → “Who you really are”

---

## Critical Mental Model

Auth Method = Entry Door  
Alias = Identity Link  
Entity = Vault Identity  

---

## Example Identity Scenario

Bryan logs in using:

✔ LDAP → username=bryan  
✔ userpass → username=bryan  

Vault sees:

Two different auth methods  
Two different login events  

Alias tells Vault:

✔ Both belong to SAME entity

---

## Entity Structure (Conceptual)

Entity:

✔ entity_id → Unique internal ID  
✔ policies → Permissions  
✔ metadata → Extra attributes  
✔ aliases → Login mappings  

---

## Why Vault Needs Entities

Without entities:

✔ Policies tied to tokens only  
✔ Harder cross-auth management  
✔ Duplicate permission models  

With entities:

✔ Centralized authorization  
✔ Identity unification  
✔ Cleaner policy management  

---

## How Vault Creates Entities Automatically

Important beginner-friendly behavior:

Vault automatically creates entities when:

✔ First successful login occurs  
✔ No existing entity matches alias  

Vault does this via:

✔ Identity Secrets Engine (built-in)

---

## What Happens During First Login?

User logs in:

✔ Vault validates credentials  
✔ Vault generates token  
✔ Vault creates entity (if needed)  
✔ Vault creates alias  

---

## Critical Clarification — Entity vs Token

Token:

✔ Temporary access credential  
✔ Has TTL  

Entity:

✔ Persistent identity object  
✔ Long-lived  
✔ Independent of tokens  

---

## Entity Metadata (Very Powerful Feature)

Entities support metadata.

Example:

✔ department=engineering  
✔ team=devops  
✔ region=india  

Why this matters:

✔ Policies can reference metadata  
✔ Dynamic authorization logic  

---

## Example Policy Using Metadata (Conceptual)

path "secret/data/{{identity.entity.metadata.team}}/*"

Meaning:

✔ Access based on entity attributes  

Very advanced capability.

---

## Multiple Auth Methods Without Entity Mapping

Important nuance.

If Bryan logs in via:

✔ LDAP  
✔ userpass  

Without alias linking:

✔ Vault creates TWO entities  

Result:

✔ Fragmented identity  
✔ Policy duplication  

---

## Why Manual Entity Creation Exists

Manual entity creation allows:

✔ Identity unification  
✔ Policy centralization  
✔ Cross-auth consolidation  

---

## Manual Entity Creation Workflow

---

## Step 1 — Verify Vault Running

Ensure Vault server active.

---

## Step 2 — View Existing Policies

    vault policy list

---

## Step 3 — Create userpass User

    vault write auth/userpass/users/bryan \
        password=bryan \
        policies=kv-policy

---

## What Happens Here

Vault:

✔ Creates login identity  
✔ Assigns kv-policy  

---

## Step 4 — Inspect Policy

    vault policy read kv-policy

Example:

    path "kv/data/automation" {
      capabilities = ["read"]
    }

---

## Step 5 — Login as User

    vault login -method=userpass username=bryan

Vault returns:

✔ Token  
✔ Entity auto-created  

---

## Step 6 — Access Allowed Path

    vault kv get kv/automation

Works due to kv-policy.

---

## Step 7 — View Auth Mount Accessors

    vault auth list

Example output:

    Path        Type      Accessor
    userpass/   userpass  auth_userpass_xxxx

---

## Why Accessor Matters

Accessor uniquely identifies:

✔ Auth method instance  

Required for alias creation.

---

## Step 8 — Create Manual Entity

    vault write identity/entity \
        name="Bryan Krausen" \
        policies=manager

---

## What Happens Here

Vault:

✔ Creates persistent identity  
✔ Assigns manager policy  

At this point:

✔ Entity exists  
✔ No aliases yet  

Entity is inactive.

---

## Step 9 — Attach Alias to Entity

    vault write identity/entity-alias \
        name="bryan" \
        canonical_id="<entity-id>" \
        mount_accessor="auth_userpass_xxxx"

---

### Parameters Explained

✔ name → External username  
✔ canonical_id → Entity ID  
✔ mount_accessor → Auth method identifier  

---

## What This Achieves

Vault now understands:

✔ userpass:bryan → Bryan Krausen entity

---

## Step 10 — Login Again

    vault login -method=userpass username=bryan

---

## Critical Result

Token now inherits:

✔ Policies from auth method  
✔ Policies from entity  

Combined capabilities.

---

## Why This Is Extremely Important

Enables:

✔ Centralized authorization  
✔ Identity normalization  
✔ Cross-auth policy aggregation  

---

## Example Combined Access

Previously:

✔ Only kv-policy  

Now:

✔ kv-policy + manager policy  

Expanded permissions.

---

## Tokens Inherit Policies From Aliases + Entity

Critical rule:

Token capabilities =

✔ Auth Method Policies  
✔ Entity Policies  

---

## Failure Scenario Without Entities

✔ Duplicate permissions  
✔ Complex policy management  
✔ Fragmented identity  

Entities eliminate this chaos.

---

## Final Mental Model — Vault Entities

Entity = Vault’s Internal Identity  
Alias = Auth Method Mapping  
Token = Temporary Access Credential  

Entities unify identity across:

✔ Multiple auth methods  
✔ Multiple login flows  
✔ Multiple environments  

---

## Critical Production Insights

✔ Always plan identity model  
✔ Avoid fragmented entities  
✔ Use metadata strategically  
✔ Link aliases carefully  
✔ Use entities for authorization logic  
✔ Monitor identity relationships  

---

## Summary

You now understand:

✔ What entities are  
✔ Why Vault needs them  
✔ Entity vs token distinction  
✔ Alias purpose  
✔ Automatic entity creation  
✔ Metadata power  
✔ Fragmentation problem  
✔ Manual entity creation  
✔ Alias linking workflow  
✔ Policy inheritance mechanics  
