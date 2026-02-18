## Installing and Running Vault Server 

Vault is **platform agnostic**, meaning it can run on many types of infrastructure.

This flexibility is one of Vault’s major strengths.

Vault can run on:

• Kubernetes (including managed services like EKS)  
• Cloud-based virtual machines (AWS, Azure, GCP)  
• VMware virtual machines  
• Physical servers  
• A developer laptop  

Vault is also available for many operating systems:

• macOS  
• Windows  
• Linux  
• FreeBSD  
• NetBSD  
• OpenBSD  
• Solaris  

---

## Prerequisite Mental Model — Vault Is Just a Binary

Vault is distributed primarily as:

✔ A single executable binary

Meaning:

• No complex installers required  
• No heavy dependencies  
• Very portable  

Installation is mostly:

Download → Extract → Configure → Run

---

## Order of Operations (Critical Workflow)

Regardless of platform, Vault setup follows this sequence:

1. Install Vault  
2. Create Configuration File  
3. Start Vault Server  
4. Initialize Vault  
5. Unseal Vault  

Beginners often skip steps or mix them — avoid that.

---

## Where to Download Vault

Official sources:

• vaultproject.io  
• releases.hashicorp.com/vault  

Always use official sources in real environments.

---

## Installation Methods Overview

Vault can be installed using:

✔ Direct binary download (most universal)  
✔ Package managers (Linux / macOS)  
✔ Helm chart (Kubernetes)  

Each approach has different use cases.

---

## Method 1 — Direct Binary Installation (Works Everywhere)

This method works on:

✔ Linux  
✔ macOS  
✔ Windows  
✔ Servers  
✔ Air-gapped systems  

---

## Step 1 — Download Vault Binary

Example (Linux):

    wget https://releases.hashicorp.com/vault/<version>/vault_<version>_linux_amd64.zip

Replace `<version>` with actual version.

---

## Step 2 — Unpack Vault

    unzip vault_<version>_linux_amd64.zip

Result:

vault executable extracted.

---

## Step 3 — Move Binary to System Path

    sudo mv vault /usr/local/bin/

---

## Why This Step Matters

Allows running Vault from anywhere:

    vault status
    vault server

Without specifying full path.

---

## Step 4 — Verify Installation

    vault --version

Confirms binary works.

---

## Method 2 — Installation via Package Manager (Linux)

Preferred for:

✔ Operational simplicity  
✔ Easier updates  
✔ Dependency management  

---

## Example — Ubuntu / Debian (APT)

    curl -fsSL https://apt.releases.hashicorp.com/gpg \
      | sudo gpg --dearmor \
      -o /etc/apt/trusted.gpg.d/hashicorp.gpg

    sudo apt-add-repository \
    "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"

    sudo apt update
    sudo apt install vault -y

---

## Why Use Package Manager?

✔ Easier upgrades  
✔ Standardized system integration  

---

## Method 3 — macOS (Homebrew)

Community-supported but common for development.

    brew install vault

Best suited for:

✔ Local learning  
✔ Development environments  

---

## Method 4 — Windows Installation

Typical flow:

1. Download Vault ZIP  
2. Extract vault.exe  
3. Add directory to PATH  

Vault behaves identically across OS.

---

## Method 5 — Kubernetes Installation (Helm)

Vault is cloud-native friendly.

Helm simplifies deployment.

---

## Step 1 — Add HashiCorp Helm Repo

    helm repo add hashicorp https://helm.releases.hashicorp.com

---

## Step 2 — Install Vault

    helm install vault hashicorp/vault

---

## What Helm Does

✔ Deploys Vault pods  
✔ Creates services  
✔ Applies configuration templates  

Important:

Helm handles infrastructure, NOT Vault initialization.

---

## Running Vault Server

Installing Vault ≠ Running Vault

Vault must be started as a server.

---

## Basic Vault Server Startup

    vault server -config=/etc/vault.d/vault.hcl

Vault reads configuration and starts.

---

## Development Mode (Learning Only)

Simplest way to experiment.

    vault server -dev

---

## What Dev Mode Does

✔ Runs Vault in memory  
✔ Auto-initializes  
✔ Auto-unseals  
✔ Generates root token  

Perfect for learning.

Never for production.

---

## Production Mode (Real Setup)

Requires configuration file.

Example minimal config:

    storage "file" {
      path = "/opt/vault/data"
    }

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

    ui = true

---

## Key Insight — Vault Server vs Vault CLI

Command confusion is very common.

Vault CLI command:

    vault status

Vault Server command:

    vault server

CLI interacts with server.

Server runs Vault system.

---

## After Vault Starts — Next Mandatory Steps

Fresh Vault server:

✔ Initialized → false  
✔ Sealed → true  

---

## Initialize Vault

    vault operator init

Generates:

✔ Unseal Keys / Recovery Keys  
✔ Root Token  

---

## Unseal Vault (Shamir Mechanism)

    vault operator unseal

Provide required key shards.

---

## Final Mental Model — Installation & Execution

Vault Setup Flow:

Install Vault Binary  
→ Create Configuration File  
→ Start Vault Server  
→ Initialize Vault  
→ Unseal Vault  
→ Login  

Same logic applies to:

✔ Laptop  
✔ Cloud VM  
✔ Physical server  
✔ Kubernetes  

---

## Critical Production Insights

✔ Use official binaries  
✔ Prefer package managers for servers  
✔ Use dev mode only for learning  
✔ TLS mandatory in production  
✔ Initialization is one-time event  
✔ Unseal mechanism defines startup behavior  

---

## Summary

You now understand:

✔ Vault platform flexibility  
✔ Supported operating systems  
✔ Installation methods  
✔ Binary vs package manager  
✔ Helm deployment basics  
✔ Dev vs production mode  
✔ Startup workflow  
