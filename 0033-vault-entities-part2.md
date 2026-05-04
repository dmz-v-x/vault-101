## Vault Entities - Part 2

## Vault Identity Secrets Engine — Deep Dive

The Identity Secrets Engine is the **core system that manages identities inside Vault**.

It is automatically enabled at:

    identity/

You do NOT need to enable it manually.

---

### What It Manages

The identity engine manages:

✔ Entities (who you are)  
✔ Aliases (how you log in)  
✔ Groups (collections of entities)  
✔ Metadata (attributes about identity)  

---

### Why Identity Engine Exists

Vault originally only had tokens.

Problem:

✔ No unified identity across auth methods  
✔ Hard to manage permissions  

Identity engine solves:

✔ Identity unification  
✔ Policy centralization  
✔ Cross-auth consistency  

---

### Core Components

1. Entity  
2. Entity Alias  
3. Group  
4. Group Alias  

---

### How It Works Internally

When user logs in:

1. Auth method validates credentials  
2. Vault checks for existing alias  
3. If not found → creates entity + alias  
4. Token is issued  
5. Policies are attached  

---

### Key API Paths

    identity/entity
    identity/entity-alias
    identity/group
    identity/group-alias

---

### Important Insight

Identity engine is:

✔ Persistent  
✔ Independent of tokens  
✔ Central to authorization  

---

## Entity Metadata & Dynamic Policies — Deep Dive

Metadata allows Vault to make **context-aware authorization decisions**.

---

### What Is Entity Metadata?

Metadata is:

✔ Key-value attributes attached to an entity

Example:

    team = devops
    region = india
    role = backend

---

### Why Metadata Matters

Instead of static policies:

✔ Policies become dynamic  
✔ Access adapts to user attributes  

---

### Example — Static Policy (Basic)

    path "secret/data/devops/*" {
      capabilities = ["read"]
    }

---

### Example — Dynamic Policy (Advanced)

    path "secret/data/{{identity.entity.metadata.team}}/*" {
      capabilities = ["read"]
    }

---

### What This Means

If:

team = devops

User can access:

    secret/data/devops/*

If:

team = finance

User can access:

    secret/data/finance/*

---

### Benefits of Metadata-Based Policies

✔ No policy duplication  
✔ Scales across teams  
✔ Reduces manual effort  
✔ Enables automation  

---

### Where Metadata Is Used

✔ Entity metadata  
✔ Token metadata  
✔ Group metadata  

---

## Groups & Identity Aggregation — Deep Dive

Groups allow you to manage **multiple identities together**.

---

### What Is a Group?

A group is:

✔ A collection of entities  
✔ Shared policy container  

---

### Why Groups Are Needed

Without groups:

✔ Must assign policies per user  
✔ Hard to scale  

With groups:

✔ Assign once → applies to many  

---

### Types of Groups

---

### 1. Internal Groups

✔ Managed inside Vault  
✔ You manually assign members  

---

### 2. External Groups

✔ Linked to external identity providers  
✔ Example: LDAP group  

Vault maps:

External group → Vault group

---

### Group Structure

Group contains:

✔ Members (entity IDs)  
✔ Policies  
✔ Metadata  

---

### Example — Create Group

    vault write identity/group \
        name="devops-team" \
        policies="devops-policy"

---

### Add Entity to Group

    vault write identity/group \
        name="devops-team" \
        member_entity_ids="<entity-id>"

---

### Result

All members inherit:

✔ Group policies  

---

### Why Groups Are Powerful

✔ Centralized permission management  
✔ Easy onboarding/offboarding  
✔ Works across auth methods  

---

## Policy Evaluation Logic — Deep Dive

This explains how Vault decides:

✔ Allow or deny access  

---

### Vault Policy Evaluation Flow

When request is made:

1. Token is validated  
2. Vault collects policies from:
   ✔ Token  
   ✔ Entity  
   ✔ Groups  
3. Vault evaluates permissions  
4. Decision: Allow or Deny  

---

### Important Rule — Deny Always Wins

If any policy says:

✔ deny → Access denied  

Even if others allow.

---

### Policy Sources (Combined)

Token policies include:

✔ Auth method policies  
✔ Entity policies  
✔ Group policies  

---

### Example Scenario

User has:

✔ policy A → allows read  
✔ policy B → denies read  

Result:

✔ Access denied  

---

### Policy Evaluation Is Path-Based

Vault checks:

✔ Path  
✔ Capability (read/write/list/etc)

---

### Example

    path "secret/data/app/*" {
      capabilities = ["read"]
    }

Vault checks:

✔ Does token allow read on path?

---

### Order Does Not Matter

Vault does NOT evaluate policies sequentially.

It merges them:

✔ Evaluates final capability set  

---

## Token Inheritance — Deep Dive

Token inheritance defines:

✔ How permissions are attached to tokens  

---

### When Token Is Created

Vault assigns:

✔ Policies from auth method  
✔ Policies from entity  
✔ Policies from groups  

---

### Token Contains:

✔ Policy list  
✔ TTL  
✔ Metadata  
✔ Entity association  

---

### Important Rule

Token capabilities =

✔ Union of all policies  

Except:

✔ Deny overrides allow  

---

### Example

User logs in via userpass:

Auth method policy → kv-policy  

Entity policy → manager  

Group policy → devops  

Final token policies:

✔ kv-policy  
✔ manager  
✔ devops  

---

### Token Lifecycle

✔ Created at login  
✔ Used for requests  
✔ Expires (TTL)  
✔ Can be renewed  
✔ Can be revoked  

---

### Token vs Entity Relationship

✔ Entity → Permanent identity  
✔ Token → Temporary access  

Multiple tokens can belong to:

✔ One entity  

---

### Why Token Inheritance Matters

✔ Simplifies permission model  
✔ Enables layered access  
✔ Supports dynamic identity  

---

## Final Mental Model — Identity System

Vault Identity System consists of:

Entity → Who you are  
Alias → How you log in  
Group → Who you belong to  
Metadata → Attributes about you  
Token → What you can do  

All combined to provide:

✔ Flexible  
✔ Scalable  
✔ Secure authorization  

---

## Summary

You now understand:

✔ Identity secrets engine role  
✔ Entity metadata and dynamic policies  
✔ Groups and aggregation  
✔ Policy evaluation logic  
✔ Token inheritance model  

---
