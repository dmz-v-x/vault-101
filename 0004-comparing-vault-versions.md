## Vault – Comparing HashiCorp Vault Versions

## Prerequisite: What Does “Vault Version” Mean?

When we talk about **Vault versions**, we are NOT talking about software releases like v1.13 or v1.14.

Instead, we are talking about **deployment and licensing models** of HashiCorp Vault:

1. Open Source Vault
2. Vault Enterprise
3. Vault on HCP (HashiCorp Cloud Platform)

All three use the **same core Vault concepts** you already learned:
- Tokens
- Policies
- Auth methods
- Secrets engines
- Paths
- TTLs

The difference is **who manages Vault** and **which advanced features are available**.

---

## Why Version Comparison Matters

Organizations choose different Vault versions based on:
- Scale
- Compliance requirements
- High availability needs
- Operational complexity
- Budget
- Security posture

Now let’s break down each version slowly and clearly.

---

## 1. Open Source Vault (Self-Hosted & Self-Managed)

### 1. What Open Source Vault Is

Open Source Vault is:
- Free
- Self-hosted
- Fully managed by your team

You install, operate, upgrade, secure, and monitor Vault yourself.

This version is ideal for:
- Learning Vault
- Small teams
- Proof of concepts
- Cost-sensitive environments

---

### 2. Core Features Available in Open Source Vault

Even the open-source version is very powerful.

It includes:

#### 2.1 Dynamic Secrets
- Generate credentials on demand
- Short-lived secrets
- Automatic revocation

#### 2.2 Access Control Policies
- Fine-grained ACL policies
- Path-based permissions
- Least privilege enforcement

#### 2.3 Init and Unseal Workflow
- Secure initialization
- Shamir key-based unsealing
- Manual or automated unseal

#### 2.4 Vault Agent
- Auto-authentication
- Secret injection
- Reduces application code complexity

#### 2.5 Key Rolling (Key Rotation)
- Automatic key rotation
- No downtime during rotation

#### 2.6 Encryption as a Service (Transit)
- Encrypt and decrypt data
- Keys never leave Vault

#### 2.7 Cloud Auto-Unseal
- AWS KMS
- Azure Key Vault
- GCP KMS

Vault can automatically unseal using cloud-native key management services.

---

### 3. Limitations of Open Source Vault

Open Source Vault does NOT include:
- Advanced replication
- Namespaces
- Disaster recovery automation
- Enterprise compliance features

Operational responsibility is entirely on your team.

---

## 2. Vault Enterprise (Self-Hosted & Self-Managed)

### 1. What Vault Enterprise Is

Vault Enterprise is:
- Commercial version
- Self-hosted
- Managed by your organization
- Includes advanced enterprise-grade features

This version is chosen by:
- Large organizations
- Regulated industries
- High-availability environments

---

### 2. Enterprise-Only Features

Vault Enterprise includes **everything from Open Source**, plus the following.

---

#### 2.1 Disaster Recovery (DR)

- Dedicated DR clusters
- Automatic failover
- Protects against region-wide failures

Critical for mission-critical systems.

---

#### 2.2 Replication

- Performance replication
- Secrets replicated across regions
- Low-latency reads

This improves global availability.

---

#### 2.3 Read Replicas

- Scale read-heavy workloads
- Reduce load on primary cluster

---

#### 2.4 Namespaces

- Logical isolation within one Vault cluster
- Each team or environment gets its own namespace
- Strong multi-tenancy support

Very important for large organizations.

---

#### 2.5 HSM Auto-Unseal

- Hardware Security Module integration
- Strong key protection
- Required in some compliance regimes

---

#### 2.6 Multi-Factor Authentication (MFA)

- Additional authentication layer
- Stronger human access security

---

#### 2.7 Sentinel Policies

- Policy-as-code
- Enforce security and compliance rules
- Prevent misconfigurations

---

#### 2.8 FIPS 140-2 Compliance

- Cryptographic compliance standard
- Required for government and regulated industries

---

#### 2.9 Seal Wrap

- Extra encryption layer
- Protects sensitive values even inside Vault

---

### 3. When to Choose Vault Enterprise

Choose Vault Enterprise if you need:
- Regulatory compliance
- Global scale
- Strong isolation
- Advanced DR and replication
- Enterprise-grade security controls

---

## 3. Vault on HCP (HashiCorp Cloud Platform)

### 1. What Vault on HCP Is

Vault on HCP is:
- Fully managed by HashiCorp
- Hosted in HashiCorp Cloud
- No infrastructure management by you

You focus on:
- Policies
- Secrets
- Access

HashiCorp handles:
- Setup
- Scaling
- Patching
- Upgrades
- Availability

---

### 2. Key Characteristics of Vault on HCP

#### 2.1 Fully Managed Solution
- No servers to manage
- No manual upgrades
- No operational burden

---

#### 2.2 Push-Button Deployment
- Create Vault clusters in minutes
- Dev and Prod options available

---

#### 2.3 Scalable by Design
- Automatically handles growth
- Built for production workloads

---

#### 2.4 Pay-by-Hour Pricing
- Usage-based pricing
- No long-term infrastructure commitment

---

#### 2.5 Includes All Enterprise Features

Vault on HCP includes:
- All Vault Enterprise features
- DR
- Replication
- Namespaces
- Compliance capabilities

Without self-managing infrastructure.

---

### 3. When to Choose Vault on HCP

Choose Vault on HCP if:
- You want zero operational overhead
- You want enterprise features immediately
- You prefer managed services
- You want faster time to production

---

## High-Level Comparison Summary

| Feature | Open Source | Enterprise | Vault on HCP |
|------|------------|------------|--------------|
| Hosting | Self | Self | HashiCorp |
| Cost | Free | Paid | Pay-as-you-go |
| Dynamic Secrets | Yes | Yes | Yes |
| Policies | Yes | Yes | Yes |
| Replication | No | Yes | Yes |
| Namespaces | No | Yes | Yes |
| DR | Limited | Advanced | Advanced |
| Managed Ops | No | No | Yes |

---

## Final Mental Model

- Open Source Vault: Learn and build
- Vault Enterprise: Scale and comply
- Vault on HCP: Scale without managing infrastructure

All three share the same core Vault concepts.
