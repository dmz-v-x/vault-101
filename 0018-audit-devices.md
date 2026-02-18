## Audit Devices in HashiCorp Vault

## Prerequisite Mental Model — Why Auditing Exists

Vault controls:

• Secrets  
• Encryption operations  
• Tokens  
• Identity  
• Policies  

Every request to Vault may impact security.

Organizations must answer:

✔ Who accessed Vault?  
✔ What did they access?  
✔ When did they access it?  
✔ Was it allowed?  

Audit Devices solve this problem.

---

## What Is an Audit Device?

An Audit Device is:

A Vault component that logs **every authenticated request and response**.

Important clarification:

Vault logs:

✔ Requests  
✔ Responses  
✔ Metadata  
✔ Operations  
✔ Errors  

This includes:

✔ Read operations  
✔ Write operations  
✔ Authentication events  
✔ Token usage  

---

## What Exactly Gets Logged?

Vault logs:

• API path accessed  
• Operation type (read/write/update/etc)  
• Request payload (sanitized)  
• Response payload (sanitized)  
• Token identity  
• Timestamp  
• Errors  

This provides a **forensic trail**.

---

## Audit Log Format — JSON

Vault audit logs are structured as:

✔ JSON

Why JSON?

✔ Machine-readable  
✔ Easy parsing  
✔ SIEM integration  
✔ Searchable  
✔ Standardized  

Audit logs are designed for automation & analysis.

---

## Sensitive Data Protection (Critical Security Feature)

Vault NEVER logs secrets in plaintext.

Instead:

✔ Sensitive values are hashed

Vault uses:

**HMAC-SHA256 with Salt**

---

## Why Hash Instead of Mask?

Hashing allows:

✔ Security protection  
✔ Future verification  

Masking would destroy comparability.

Hashing preserves:

✔ Privacy  
✔ Integrity  

---

## How Vault Hashing Works

Sensitive data:

Secret → Hashed via HMAC-SHA256 → Stored in log

Vault retains ability to verify values later.

---

## Important Security Insight — /sys/audit-hash API

Vault provides:

/sys/audit-hash endpoint

Purpose:

✔ Hash a value using Vault’s salt  
✔ Compare with audit logs  

Used for:

✔ Incident investigation  
✔ Validation  
✔ Forensics  

---

## Critical Warning — Protect Audit Logs

Even though values are hashed:

✔ Logs are still sensitive

Why?

Because an attacker with:

✔ Log access  
✔ Audit-hash API access  

Can verify secrets.

Thus:

✔ Audit logs must be strictly protected

---

## Audit Device Types

Vault supports multiple audit device mechanisms.

Each serves different operational needs.

---

## 1. File Audit Device

Most common and simplest.

Example configuration:

    vault audit enable file file_path=/var/log/vault_audit.log

---

### Characteristics

✔ Appends logs to file  
✔ Persistent  
✔ Simple setup  

---

### Limitations

✔ Vault does NOT handle log rotation  

Must use external tools:

✔ logrotate  
✔ fluentd  
✔ filebeat  

---

### Best Practice

Use log forwarding tools:

✔ Send logs to centralized collector  

Never rely on local logs only.

---

## 2. Syslog Audit Device

Example:

    vault audit enable syslog

---

### Characteristics

✔ Writes logs to Syslog  
✔ Uses OS logging pipeline  

---

### Limitations

✔ Sends only to local syslog agent  

Requires syslog forwarding configuration.

---

### When Useful

✔ Traditional infrastructure  
✔ Centralized syslog environments  

---

## 3. Socket Audit Device

Example:

    vault audit enable socket address=127.0.0.1:9000 socket_type=tcp

---

### Characteristics

✔ Writes logs to socket  
✔ Supports TCP / UDP / Unix sockets  

---

### Reliability Considerations

✔ UDP = Unreliable  
✔ TCP = More reliable  

UDP may lose logs → Security risk.

---

### When Useful

✔ High-performance pipelines  
✔ Streaming log architectures  

---

## Choosing Audit Device Type — Logical Thinking

Instead of memorizing types, think operationally.

---

## Need Simplicity?

✔ File

---

## Using Traditional OS Logging?

✔ Syslog

---

## Using Log Streaming / Collectors?

✔ Socket (TCP preferred)

---

## Critical Vault Behavior — Safety Over Availability

This is extremely important.

If Audit Devices are enabled:

✔ Vault REQUIRES successful log write  
✔ BEFORE completing request  

Meaning:

Vault Flow:

1. Client request arrives  
2. Vault validates request  
3. Vault writes audit log  
4. Only then returns response  

---

## Why Vault Does This

Security principle:

✔ Never allow unlogged secret access

Audit integrity > Availability

---

## What Happens If Logging Fails?

Vault will:

✔ Reject client requests  
✔ Stop responding  

Vault intentionally fails closed.

---

## Beginner Confusion (Very Common)

Wrong assumption:

“If audit logging fails, Vault continues.”

Incorrect.

Vault chooses:

✔ Security correctness over uptime

---

## Why This Design Matters

Prevents:

✔ Silent data access  
✔ Undetected misuse  
✔ Audit trail gaps  

---

## Multiple Audit Devices — Strong Best Practice

Vault supports:

✔ Multiple audit devices

Example:

    vault audit enable file file_path=/var/log/vault_audit.log
    vault audit enable syslog

---

## Why Use Multiple Devices?

✔ Redundancy  
✔ Failure protection  
✔ Independent log paths  

Critical for:

✔ Production environments  
✔ Compliance systems  

---

## Vault Requirement — At Least One Audit Device (If Enabled)

Important nuance:

Vault does NOT require audit devices by default.

But:

✔ Once enabled → Logging becomes mandatory

---

## Practical Hands-On Workflow

---

## Step 1 — Enable File Audit Device

    vault audit enable file file_path=/var/log/vault_audit.log

---

## Step 2 — Verify Enabled Devices

    vault audit list

---

## Step 3 — Perform Vault Operation

    vault kv get secrets/myapp/config

Vault logs:

✔ Request  
✔ Response  

---

## Step 4 — Inspect Log File

    cat /var/log/vault_audit.log

Observe:

✔ JSON structure  
✔ Hashed values  

---

## Final Mental Model — Audit Devices

Audit Devices provide:

✔ Security visibility  
✔ Forensic trail  
✔ Compliance support  
✔ Breach investigation capability  

Vault ensures:

✔ No authenticated request goes unlogged

Audit system = Security backbone

---

## Critical Production Insights

✔ Always enable auditing in production  
✔ Protect audit logs strictly  
✔ Use centralized log collectors  
✔ Prefer TCP over UDP  
✔ Use multiple audit devices  
✔ Monitor audit pipeline health  

---

## Summary

You now understand:

✔ What audit devices are  
✔ Why they exist  
✔ What Vault logs  
✔ JSON log structure  
✔ Sensitive data hashing  
✔ Audit-hash API purpose  
✔ Device types  
✔ Failure behavior  
✔ Redundancy best practices  
