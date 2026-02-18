## Different Ways of Installing and Running HashiCorp Vault

## Prerequisite: What Does “Installing and Running Vault” Mean?

Installing Vault means:
- Getting the Vault binary or service on your system

Running Vault means:
- Starting the Vault server
- Providing configuration
- Choosing storage
- Deciding how Vault will be unsealed
- Deciding how clients will connect

Vault is **not just a CLI tool**.  
Vault is primarily a **server process** that clients (CLI, UI, API) talk to.

---

## High-Level Ways to Run Vault

There are **five common ways** to install and run Vault:

1. Local development mode
2. Binary installation (manual)
3. Package manager installation
4. Container-based (Docker / Kubernetes)
5. Managed Vault (HCP Vault)

We will go through each one step by step.

---

## 1. Vault Dev Mode (For Learning and Testing Only)

### 1. What Dev Mode Is

Dev mode is:
- A single-command Vault server
- In-memory storage
- Automatically initialized
- Automatically unsealed

This mode is **NOT secure** and **NOT persistent**.

---

### 2. Why Dev Mode Exists

Dev mode exists to:
- Learn Vault concepts
- Test commands
- Experiment locally
- Avoid setup complexity

Every restart wipes all data.

---

### 3. How Dev Mode Works Internally

In dev mode:
- Storage is in memory
- A root token is auto-generated
- TLS is disabled
- No unseal keys exist

This removes all security barriers for learning.

---

### 4. When to Use Dev Mode

Use dev mode only when:
- You are learning Vault
- You are following tutorials
- You are testing basic flows

Never use dev mode in production.

---

## 2. Installing Vault Using the Official Binary

### 1. What This Method Is

This is the most **fundamental and transparent** way to install Vault.

You:
- Download the Vault binary
- Place it on the system
- Configure everything manually

This method works on:
- Linux
- macOS
- Windows

---

### 2. What You Need Beforehand

Prerequisites:
- Basic command-line knowledge
- Ability to manage files
- Understanding of system services (for production)

---

### 3. How This Installation Works Conceptually

Steps involved:
1. Download Vault binary
2. Place it in PATH
3. Create Vault configuration file
4. Choose storage backend
5. Start Vault server

This gives you full control and visibility.

---

### 4. When to Use Binary Installation

Use this method when:
- You want full control
- You are running Vault on VMs
- You want minimal abstraction
- You are setting up production Vault yourself

---

## 3. Installing Vault Using Package Managers

### 1. What This Method Is

HashiCorp provides official repositories for:
- APT (Ubuntu/Debian)
- YUM/DNF (RHEL/CentOS/Amazon Linux)

Vault is installed like any other system package.

---

### 2. What Package Manager Installation Gives You

Benefits:
- Easier installation
- Version management
- Cleaner upgrades
- Systemd service files included

Vault runs as a system service.

---

### 3. Operational Considerations

Even though installation is easier:
- You still manage configuration
- You still manage storage
- You still manage unseal process
- You still manage upgrades

This is common for on-prem and VM-based deployments.

---

### 4. When to Use Package Manager Installation

Use this when:
- You run Linux servers
- You want OS-native management
- You are running Vault long-term on VMs

---

## 4. Running Vault Using Docker

### 1. What Containerized Vault Means

Vault can run as a Docker container.

In this model:
- Vault runs inside a container
- Configuration is mounted as files
- Storage is external (never container filesystem)

---

### 2. Important Prerequisite for Docker Vault

You must understand:
- Containers are ephemeral
- Vault data must be stored outside the container
- Vault must not lose storage

Docker is fine for:
- Local testing
- CI pipelines
- Some controlled environments

---

### 3. How Vault Runs in Docker Conceptually

- Vault binary runs inside container
- Exposes HTTP API
- Uses external storage backend
- Requires unseal like normal Vault

---

### 4. When Docker Vault Makes Sense

Use Docker Vault when:
- You already use containers
- You want reproducible environments
- You are testing Vault behavior

Be cautious for production unless you understand storage and security well.

---

## 5. Running Vault on Kubernetes

### 1. Why Kubernetes Changes Things

Kubernetes introduces:
- Pod restarts
- Scaling
- Scheduling
- Dynamic networking

Vault must be designed carefully in Kubernetes.

---

### 2. Common Kubernetes Vault Setup

Typical approach:
- Vault runs as StatefulSet
- Uses persistent volumes
- Uses Raft or external storage
- Uses cloud KMS for auto-unseal

Vault integrates deeply with Kubernetes authentication.

---

### 3. Helm-Based Installation

Most Kubernetes deployments use:
- HashiCorp Vault Helm chart

This provides:
- Production-ready templates
- TLS support
- Auto-unseal configuration
- HA support

---

### 4. When to Run Vault on Kubernetes

Use Kubernetes Vault when:
- Your infrastructure is Kubernetes-native
- Your applications run in Kubernetes
- You need Kubernetes auth integration

This is a very common modern setup.

---

## 6. Vault on HCP (Fully Managed Vault)

### 1. What Vault on HCP Is

Vault on HCP is:
- Hosted by HashiCorp
- Fully managed
- No servers to maintain

You do NOT:
- Install Vault
- Manage upgrades
- Handle HA
- Handle DR

---

### 2. What You Still Control

You still control:
- Auth methods
- Policies
- Secrets
- Access models

HashiCorp controls:
- Infrastructure
- Availability
- Scaling
- Patching

---

### 3. When to Choose Vault on HCP

Choose HCP Vault when:
- You want fastest production readiness
- You want enterprise features
- You want minimal operational overhead
- You trust managed services

---

## Installation Method Comparison (Mental Model)

| Method | Learning | Production | Ops Effort |
|------|--------|-----------|-----------|
| Dev Mode | Yes | No | None |
| Binary | Yes | Yes | High |
| Package Manager | Yes | Yes | Medium |
| Docker | Yes | Maybe | Medium |
| Kubernetes | No | Yes | High |
| HCP Vault | No | Yes | Very Low |

---

## Final Guidance

- Learning → Dev mode
- Small setup → Binary or package manager
- Containers → Docker or Kubernetes
- Enterprise scale → Vault Enterprise or HCP Vault

All methods run the **same Vault internally**.  
Only deployment and responsibility change.
