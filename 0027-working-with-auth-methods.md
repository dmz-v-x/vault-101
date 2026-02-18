## Working with Auth Methods 

## Prerequisite Mental Model — Auth Methods Are Pluggable

Vault authentication is **modular**.

Meaning:

✔ Auth methods are NOT all active by default  
✔ Most must be explicitly enabled  
✔ Multiple methods can coexist  

Vault behaves like:

Authentication Framework + Plugins

---

## Why Auth Methods Must Be Enabled First

Vault is security-focused.

Instead of enabling everything automatically:

✔ You explicitly choose identity mechanisms  
✔ You reduce attack surface  
✔ You control trust boundaries  

---

## Default Authentication State of a New Vault

Fresh Vault deployment:

✔ Only Token Auth Method exists  
✔ Root Token is primary credential  

Important clarification:

This does NOT mean token auth is “optional”.

Tokens are Vault’s core mechanism.

---

## Token Auth Method — Special Case

Token auth method:

✔ Enabled by default  
✔ Cannot be disabled  
✔ Cannot be re-enabled  
✔ Fundamental to Vault  

Why?

Vault internally relies on tokens.

Disabling token auth would break Vault.

---

## Root Token — Initial Authentication Mechanism

After Vault initialization:

✔ Root Token is generated  

This token allows:

✔ Full administrative access  
✔ Enabling auth methods  
✔ Creating policies  
✔ Managing Vault  

Without root token → Vault cannot be configured.

---

## Critical Rule — Admin Actions Require Valid Token

To:

✔ Enable auth methods  
✔ Disable auth methods  
✔ Modify auth methods  

You MUST provide:

✔ Valid token  
✔ Proper privileges  

Vault never allows anonymous configuration.

---

## Vault Interfaces & Auth Management

Auth methods can be managed via:

✔ CLI (most common)  
✔ HTTP API (most powerful)  
✔ UI (limited capabilities)

---

## Why UI Is Limited

UI prioritizes:

✔ Common workflows  
✔ Simplicity  

Advanced operations often require:

✔ CLI  
✔ API  

---

## Auth Methods Can Coexist

Vault allows:

✔ One auth method  
✔ Many auth methods  

Example:

✔ LDAP for humans  
✔ AppRole for applications  
✔ AWS IAM for cloud workloads  

This flexibility is a major Vault strength.

---

## Why Multiple Auth Methods Are Common

Different actors require different identity models.

Humans → Enterprise identity providers  
Applications → Machine identity systems  

---

## Each Auth Method Is Mounted at a Path

Critical Vault design principle:

**Everything in Vault is Path-Based**

Auth methods are no exception.

---

## Example Enable Command

    vault auth enable approle

Vault response:

Success! Enabled approle auth method at: approle/

---

## What Just Happened Internally?

Vault:

✔ Loaded AppRole auth plugin  
✔ Mounted it at path → approle/  
✔ Registered routes  

Now available endpoints:

/auth/approle/login  
/auth/approle/role  
/auth/approle/config  

---

## Choosing Custom Mount Paths (Very Important)

You may specify your own path:

    vault auth enable -path=my-custom-approle approle

Now mounted at:

my-custom-approle/

---

## Why Custom Paths Are Useful

✔ Multiple instances of same auth type  
✔ Environment separation  
✔ Logical organization  
✔ Migration strategies  

Example:

✔ approle-dev/  
✔ approle-prod/

---

## Critical Rule — Path Cannot Be Changed Later

Once mounted:

✔ Path is permanent

Vault does NOT support renaming.

---

## Why Path Renaming Is Not Allowed

Paths are deeply tied to:

✔ Policies  
✔ Tokens  
✔ Integrations  
✔ Automation scripts  

Renaming could break security assumptions.

---

## How to Change Path (Correct Approach)

If path must change:

1. Disable auth method  
2. Re-enable with new path  

Example:

    vault auth disable approle

    vault auth enable -path=new-approle approle

---

## Default Mount Path Behavior

If no path specified:

✔ Vault uses default path

Rule:

Default Path = Auth Method Type

Examples:

✔ aws → aws/  
✔ approle → approle/  
✔ ldap → ldap/  

---

## Viewing Enabled Auth Methods

    vault auth list

Example output:

Path        Type        Accessor
----        ----        --------
token/      token       auth_token_xxxx
approle/    approle     auth_approle_xxxx

---

## What This Output Means

✔ Path → Where auth method lives  
✔ Type → Authentication mechanism  
✔ Accessor → Internal identifier  

---

## Disabling Auth Methods

    vault auth disable approle

Vault:

✔ Removes mount  
✔ Deletes configuration  
✔ Revokes associated tokens (important nuance)

---

## Important Safety Insight

Disabling auth method may affect:

✔ Active users  
✔ Running applications  
✔ Issued tokens  

Must be done carefully.

---

## Configuring Auth Methods (Conceptual Example)

Enabling ≠ Ready to use

Most auth methods require configuration.

Example AppRole:

✔ Define roles  
✔ Attach policies  

Example LDAP:

✔ Configure LDAP server  
✔ Bind credentials  

---

## Vault Security Rule — Token Privileges Required

To manage auth methods:

✔ Token must include proper capabilities

Typically controlled by policies:

Example:

path "sys/auth/*" {
  capabilities = ["create", "read", "update", "delete", "sudo"]
}

---

## Why This Restriction Exists

Prevents:

✔ Unauthorized identity modification  
✔ Privilege escalation  
✔ Security bypass  

---

## Final Mental Model — Working with Auth Methods

Auth Methods = Identity Entry Points

Mounted at Paths  
Managed via Tokens  
Governed by Policies  

Lifecycle:

Enable → Configure → Use → Disable (if needed)

---

## Critical Production Insights

✔ Always plan mount paths carefully  
✔ Avoid frequent disable/re-enable  
✔ Use different auth methods per use case  
✔ Restrict sys/auth permissions  
✔ Test changes in lower environments  

---

## Summary

You now understand:

✔ Why auth methods must be enabled  
✔ Default token auth behavior  
✔ Root token role  
✔ Token privilege requirements  
✔ Mount path mechanics  
✔ Custom paths  
✔ Path immutability  
✔ Listing auth methods  
✔ Disabling auth methods  
✔ Configuration necessity  

