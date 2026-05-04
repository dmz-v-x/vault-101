## Vault Policies

Vault Policies are the **core authorization mechanism** in Vault.

If Auth Methods answer:

✔ Who are you?

Then Policies answer:

✔ What are you allowed to do?

Without policies:

✔ Vault is unusable  
✔ No access is granted  
✔ Everything is denied  

---

## Prerequisite Mental Model — Authentication vs Authorization

Before policies, understand this clearly:

✔ Authentication → Identity (who you are)  
✔ Authorization → Permissions (what you can do)  

Vault Policies handle:

✔ Authorization

---

## What Is a Vault Policy?

A Vault Policy is:

A set of rules that define:

✔ Which paths you can access  
✔ What actions you can perform  

Policies are:

✔ Declarative  
✔ Path-based  
✔ Capability-driven  

---

## What Do Policies Control?

Policies define access to:

✔ Secrets  
✔ Auth methods  
✔ System endpoints  
✔ Tokens  
✔ Identity  

Everything in Vault is protected by policies.

---

## Policy Language

Policies can be written in:

✔ HCL (most common)  
✔ JSON  

---

## Example Policy (HCL)

    path "secret/data/myapp/*" {
      capabilities = ["read"]
    }

---

## What This Means

User can:

✔ Read secrets under:

    secret/data/myapp/*

Cannot:

✔ Write  
✔ Delete  
✔ List  

---

## Capabilities in Vault Policies

Capabilities define actions.

Common capabilities:

✔ create  
✔ read  
✔ update  
✔ delete  
✔ list  
✔ sudo  

---

## Capability Meaning (Simple)

✔ read → fetch data  
✔ create → add new data  
✔ update → modify data  
✔ delete → remove data  
✔ list → view keys  
✔ sudo → admin-level operations  

---

## Principle of Least Privilege (Very Important)

Vault strongly follows:

**Least Privilege Principle**

Meaning:

✔ Give only required permissions  
✔ Nothing extra  

---

## Why Least Privilege Matters

Reduces:

✔ Security risks  
✔ Blast radius  
✔ Accidental misuse  

---

## Vault Policy Behavior — Deny by Default

This is the MOST IMPORTANT RULE.

Vault is:

✔ Deny by default (implicit deny)

Meaning:

✔ No policy → No access  

---

## Example

If user has no policy:

✔ Cannot read anything  
✔ Cannot write anything  

Vault blocks everything.

---

## Explicit Allow Required

To allow access:

✔ You must explicitly define:

✔ Path  
✔ Capability  

---

## Explicit Deny (Overrides Everything)

Vault supports:

✔ Explicit deny

Example:

    path "secret/data/finance/*" {
      capabilities = ["deny"]
    }

---

## Critical Rule

✔ deny ALWAYS overrides allow

Even if another policy allows access.

---

## Policy Attachment — How Policies Are Used

Policies are NOT applied directly to users.

They are attached to:

✔ Tokens  

---

## Token-Based Authorization

When token is issued:

✔ It carries policies  

Vault evaluates:

✔ Token policies → Allow or Deny  

---

## Multiple Policies Per Token

A token can have:

✔ One policy  
✔ Multiple policies  

---

## Policy Behavior — Cumulative & Additive

Vault combines policies.

Example:

Policy A:

✔ read access  

Policy B:

✔ write access  

Result:

✔ read + write access  

---

## Important Rule

✔ Capabilities are additive  
✔ Except deny (which overrides)

---

## Built-in Vault Policies

Vault includes default policies.

---

## 1. Root Policy

✔ Superuser policy  
✔ Full access to everything  

---

### Characteristics

✔ Cannot be modified  
✔ Cannot be deleted  
✔ Attached to root tokens  

---

### Important Note

You cannot read root policy:

    vault policy read root

Output:

No policy named root

---

## Why Root Policy Cannot Be Read

For security reasons:

✔ Prevents exposure  
✔ Prevents misuse  

---

## 2. Default Policy

✔ Assigned to all non-root tokens  

---

### Characteristics

✔ Can be modified  
✔ Cannot be deleted  

---

### What Default Policy Does

Provides minimal permissions:

✔ Token self-lookup  
✔ Basic identity access  

---

## Listing Policies

    vault policy list

---

### Example Output

    default
    root
    kv-policy
    manager

---

## Reading a Policy

    vault policy read default

---

### Output Shows

✔ Paths  
✔ Capabilities  

---

## Example Policy Breakdown

    path "kv/data/automation" {
      capabilities = ["read"]
    }

---

### Interpretation

✔ Can read automation secrets  
✔ Cannot write/delete  

---

## Why Policies Are Powerful

Policies provide:

✔ Fine-grained control  
✔ Path-level security  
✔ Capability-level control  

---

## Real-World Example

Application needs:

✔ Read DB credentials  

Policy:

    path "database/creds/app-role" {
      capabilities = ["read"]
    }

---

## No Access Elsewhere

✔ Cannot access other paths  
✔ Cannot modify data  

---

## Policy Evaluation Flow

When request is made:

1. Token presented  
2. Vault extracts policies  
3. Vault checks path  
4. Vault checks capability  
5. Decision: Allow / Deny  

---

## Example Evaluation

User tries:

    vault kv get secret/data/app

Vault checks:

✔ Does policy allow read on path?  

If yes → Allowed  
If no → Denied  

---

## Common Beginner Mistakes

---

### No Policy Assigned

Result:

✔ 403 Access Denied  

---

### Wrong Path Format

KV v2 paths must include:

✔ data/

Incorrect:

    secret/app

Correct:

    secret/data/app

---

### Missing Capability

If policy only allows read:

✔ Cannot write  

---

## Final Mental Model — Vault Policies

Policies = Authorization Rules

Vault:

✔ Deny everything by default  
✔ Allow only what is explicitly defined  
✔ Combine policies  
✔ Enforce least privilege  

---

## Critical Production Insights

✔ Always follow least privilege  
✔ Avoid overly broad policies  
✔ Use explicit deny carefully  
✔ Test policies before applying  
✔ Separate policies per use case  
✔ Avoid using root token  
