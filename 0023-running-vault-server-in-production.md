## Vault Server in Production

## Prerequisite Mental Model — Production Vault Goals

Production Vault must guarantee:

• Secrets are never lost  
• Vault is highly available  
• Traffic is encrypted  
• Failures are handled gracefully  
• Startup is automated  
• Security is strict  

Dev mode intentionally ignores most of these.

---

## Core Characteristics of Production Vault

Production Vault requires:

✔ Persistent Storage Backend  
✔ Configuration File  
✔ TLS Encryption  
✔ Service Manager  
✔ Proper Unseal Mechanism  
✔ Monitoring & Logging  

Without these → Vault is not production-ready.

---

## Deploying Vault Nodes in Production

Vault production deployments typically involve:

✔ One or more persistent nodes  
✔ Configuration-driven startup  

Vault behavior is defined via:

Configuration File

Example startup:

    vault server -config=/etc/vault.d/vault.hcl

---

## Why Configuration File Is Mandatory

Production Vault must explicitly define:

✔ Storage backend  
✔ Listener configuration  
✔ TLS certificates  
✔ Seal mechanism  
✔ Cluster parameters  

No implicit defaults allowed.

---

## Storage Backend Requirements

Production Vault needs:

✔ Persistent storage  
✔ Reliability  
✔ HA support (recommended)  

Common production backends:

✔ Raft Integrated Storage (modern default)  
✔ Consul (widely used in enterprises)

---

## Multiple Vault Nodes — High Availability Model

Production Vault clusters often include:

Vault Node A  
Vault Node B  
Vault Node C  

Characteristics:

✔ One active node  
✔ Other standby nodes  
✔ Shared storage backend  

Benefits:

✔ Redundancy  
✔ Failover capability  
✔ Higher reliability  

---

## Deploy Vault Close to Applications

Critical architecture principle:

✔ Reduce network latency  
✔ Improve reliability  
✔ Minimize failure domains  

Vault should live near:

✔ Applications  
✔ Services  
✔ Infrastructure components  

---

## Automation Is Almost Always Used

Real production environments automate:

✔ Vault provisioning  
✔ Configuration deployment  
✔ TLS setup  
✔ Service startup  

Manual operations do not scale.

---

## Running Vault via Service Manager

Production Vault should NEVER be started manually.

Instead:

✔ Managed by service manager

Examples:

✔ systemd (Linux)  
✔ Windows Service Manager  

Benefits:

✔ Automatic restart  
✔ Crash recovery  
✔ Centralized control  

---

## Example Vault Service Management (Linux)

Start Vault:

    sudo systemctl start vault

Stop Vault:

    sudo systemctl stop vault

Check Status:

    sudo systemctl status vault

---

## Why Service Manager Matters

Without it:

✔ Vault dies → Vault stays down  

With it:

✔ Vault dies → Vault restarts automatically  

---

## Single Node Production Vault (Not Recommended)

Characteristics:

✔ No redundancy  
✔ No failover  
✔ No scalability  

Acceptable ONLY for:

✔ Testing  
✔ Small experiments  

Never for critical systems.

---

## Multi-Node Production Vault Cluster (Recommended)

Two common models exist.

---

## Model 1 — Integrated Storage (Raft)

Each Vault node:

✔ Has its own storage directory  
✔ Participates in Raft consensus  
✔ Replicates data internally  

Benefits:

✔ No external dependencies  
✔ Simpler architecture  
✔ Built-in HA  

---

### Example Raft Configuration

    storage "raft" {
      path    = "/opt/vault/data"
      node_id = "node-a"
    }

---

## Model 2 — External Storage Backend (Consul)

Vault nodes:

✔ Share a common Consul backend  
✔ Use Consul for coordination & storage  

Benefits:

✔ Mature ecosystem  
✔ Strong HA primitives  
✔ Widely adopted  

---

### Example Consul Configuration

    storage "consul" {
      address = "127.0.0.1:8500"
      path    = "vault/"
    }

---

## Important Architecture Insight

Integrated Storage:

Vault handles storage & HA

External Backend:

Storage system handles HA primitives

---

## Step-by-Step Manual Production Install (Logical Flow)

Production deployments follow a structured workflow.

---

## Step 1 — Download Vault Binary

    curl -Lo vault.zip https://releases.hashicorp.com/vault/<version>/vault_<version>_linux_amd64.zip

---

## Step 2 — Extract Vault

    unzip vault.zip

---

## Step 3 — Move Vault Binary

    sudo mv vault /usr/local/bin/

---

## Step 4 — Create Configuration File

Example:

    sudo vi /etc/vault.d/vault.hcl

---

## Example Minimal Production Config (Learning)

    storage "file" {
      path = "/opt/vault/data"
    }

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

    ui = true

---

## Production Note

Real production requires:

✔ TLS enabled  
✔ Proper certificates  

---

## Step 5 — Create systemd Service File

    sudo vi /etc/systemd/system/vault.service

---

## Example Service File

    [Unit]
    Description=Vault
    Requires=network-online.target
    After=network-online.target

    [Service]
    User=vault
    Group=vault
    ExecStart=/usr/local/bin/vault server -config=/etc/vault.d/vault.hcl
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

---

## Step 6 — Reload systemd

    sudo systemctl daemon-reload

---

## Step 7 — Start Vault Service

    sudo systemctl start vault

---

## Step 8 — Check Vault Status

    vault status

Expected:

Initialized → false  
Sealed → true  

---

## Step 9 — Initialize Vault

    vault operator init

---

## Step 10 — Unseal Vault

    vault operator unseal

Provide required keys.

---

## Final Mental Model — Production Vault

Production Vault = Security Infrastructure Component

Requires:

✔ Persistent storage  
✔ TLS encryption  
✔ Service manager  
✔ HA design  
✔ Monitoring  
✔ Automation  

Dev Mode = Playground  
Production Mode = Critical System

---

## Critical Production Insights

✔ Single node Vault is fragile  
✔ HA clusters are standard  
✔ Raft is modern preferred backend  
✔ TLS is mandatory  
✔ Automation is essential  
✔ Service manager is required  

---

## Summary

You now understand:

✔ Production Vault goals  
✔ Why config file required  
✔ Storage backend implications  
✔ Single vs multi-node architecture  
✔ Integrated vs external storage  
✔ Service manager importance  
✔ Manual production workflow  
