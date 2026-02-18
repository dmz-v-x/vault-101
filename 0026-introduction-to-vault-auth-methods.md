## Introduction to Vault Auth Methods

## Prerequisite Mental Model — Vault Trust Model

Vault follows a strict rule:

**Everything requires a token**

Before Vault allows any operation, it must answer:

✔ Who are you?  
✔ Should you be trusted?  
✔ What are you allowed to do?  

Auth Methods solve this identity problem.

---

## What Is an Auth Method?

An Auth Method is:

A Vault component responsible for:

✔ Authenticating users or systems  
✔ Validating identity  
✔ Issuing Vault tokens  
✔ Mapping identity → policies  

Important:

Auth Methods DO NOT directly grant access.

They grant:

✔ Tokens

Tokens enforce access.

---

## Fundamental Goal of All Auth Methods

This is the single most important concept:

**The goal of every auth method = Obtain a Vault Token**

Everything else is secondary.

No token → No Vault usage.

---

## Why Vault Uses Tokens Instead of Sessions

Vault is designed for:

✔ Distributed systems  
✔ APIs  
✔ Machines  
✔ Automation  

Tokens provide:

✔ Stateless authentication  
✔ TTL-based expiration  
✔ Fine-grained policies  
✔ Revocation capability  

---

## Tokens — Core Authentication Mechanism

Vault’s security model revolves around:

✔ Tokens

Most Vault operations require:

✔ Existing valid token

---

## Important Clarification — Auth Methods vs Tokens

Auth Methods:

✔ Validate identity  
✔ Issue tokens  

Tokens:

✔ Carry permissions  
✔ Control access  
✔ Enforce policies  

---

## Example Mental Flow

User logs in → Vault validates identity → Vault issues token → Token used for requests

---

## Multiple Auth Methods Can Be Enabled

Vault supports many auth mechanisms.

Why?

Different environments require different identity systems.

Examples:

✔ Username / Password  
✔ LDAP  
✔ AppRole  
✔ Kubernetes  
✔ AWS IAM  
✔ OIDC  
✔ TLS Certificates  

---

## Human vs System Authentication

Very important distinction.

---

## Human-Oriented Auth Methods

Designed for interactive users.

Examples:

✔ LDAP  
✔ OIDC  
✔ Username / Password  

---

## Machine-Oriented Auth Methods

Designed for applications/services.

Examples:

✔ AppRole  
✔ Kubernetes  
✔ AWS IAM  

---

## Why Separation Matters

Humans:

✔ Log in occasionally  
✔ Interactive workflows  

Machines:

✔ Authenticate automatically  
✔ Frequent requests  
✔ No manual login  

---

## Vault Token Properties (Critical Understanding)

Every Vault token has:

✔ Policies  
✔ TTL (Time To Live)  
✔ Metadata  
✔ Renewable / Non-renewable  
✔ Revocable  

---

## TTL — Why Tokens Expire

Vault tokens are NOT permanent by default.

TTL provides:

✔ Automatic expiration  
✔ Reduced risk  
✔ Better security posture  

---

## Example

Short-lived token:

✔ Lower compromise window

---

## What Happens After Authentication?

Successful authentication results in:

✔ Vault Token Issued

All future requests must use:

✔ That token

---

## Vault Has No Redirect-Based Authentication

Critical design principle.

Vault does NOT behave like a web app.

If request lacks token:

✔ No login prompt  
✔ No redirect  
✔ No suggestions  

Vault responds:

✔ 403 Access Denied

---

## Why Vault Behaves This Way

Vault is:

✔ API-first  
✔ Machine-first  
✔ Security-focused  

Redirects break automation.

---

## Token Auth Method (Special Case)

Vault includes a built-in auth method:

✔ token auth method

---

## What Token Auth Method Does

Responsible for:

✔ Creating tokens  
✔ Managing tokens  
✔ Storing tokens  

---

## Critical Rule — Token Auth Method Cannot Be Disabled

Why?

Because:

✔ Vault internally depends on tokens  

Disabling token auth = Breaking Vault itself.

---

## External Identity Authentication Still Produces Tokens

Even when using:

✔ LDAP  
✔ OIDC  
✔ AWS  

Vault STILL issues:

✔ Vault Tokens

Tokens remain universal access mechanism.

---

## Auth Methods — Full Workflow (Step-by-Step)

This is the fundamental lifecycle.

---

## Step 1 — User/System Provides Credentials

Examples:

✔ Username & Password  
✔ IAM Signature  
✔ RoleID & SecretID  
✔ JWT Token  

---

## Step 2 — Vault Validates Credentials

Vault forwards validation to:

✔ Identity provider / backend

Examples:

✔ LDAP server  
✔ Cloud IAM  
✔ Kubernetes API  

---

## Step 3 — Vault Generates Token

Vault attaches:

✔ Policies  
✔ TTL  
✔ Metadata  

---

## Step 4 — Token Returned to Client

Client now holds:

✔ Vault Token

---

## Step 5 — Client Uses Token for Requests

Example:

✔ Read secrets  
✔ Write secrets  
✔ Generate credentials  

---

## Example Real-World Flow (Conceptual)

Application:

1. Authenticates via AppRole  
2. Receives Vault Token  
3. Uses token to fetch DB credentials  

---

## Critical Vault Principle — Auth ≠ Access

Auth Methods:

✔ Verify identity

Policies:

✔ Define permissions

Tokens:

✔ Carry policies

---

## Final Mental Model — Auth Methods

Auth Methods = Identity Verification Layer

Vault Token = Access Mechanism

Policies = Permission System

TTL = Security Control

---

## Critical Production Insights

✔ Choose auth methods based on use case  
✔ Machines → Prefer non-human auth  
✔ Avoid static credentials when possible  
✔ Tokens should be short-lived  
✔ Always enforce least privilege  

---

## Summary

You now understand:

✔ What auth methods are  
✔ Why Vault needs them  
✔ Token-centric design  
✔ Fundamental goal of authentication  
✔ Human vs machine auth  
✔ Token properties  
✔ 403 behavior  
✔ Token auth method role  
✔ Full authentication workflow  

