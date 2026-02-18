## Configuring the Consul Storage Backend 

## Prerequisite Mental Model — Why Vault Needs Consul

Vault requires:

✔ Durable persistent storage  
✔ High availability coordination  
✔ Leader election support  
✔ Data consistency  

Consul provides exactly these capabilities.

---

## What Consul Provides for Vault

Consul offers:

✔ Durable Key/Value Storage  
✔ High Availability  
✔ Distributed Consensus  
✔ Independent Scalability  
✔ Built-in Snapshots  
✔ HashiCorp Supported Integration  

Consul becomes:

Vault’s Persistent Brain

---

## Critical Consul Mental Model

Consul is:

✔ A distributed system  
✔ Multi-node cluster  
✔ Uses consensus protocol  
✔ Elects a leader  

Cluster characteristics:

✔ Odd number of nodes preferred  
✔ Data replicated to all members  
✔ Fault tolerant  

---

## Why Odd Number of Nodes?

Consensus systems require majority voting.

Example:

3 nodes → Majority = 2  
5 nodes → Majority = 3  

Even numbers increase split-brain risk.

---

## Leader Election in Consul

Consul cluster:

✔ One leader  
✔ Other followers  

Leader:

✔ Accepts writes  
✔ Replicates logs  

Followers:

✔ Maintain consistency  
✔ Ready for failover  

---

## Important Production Rule

Consul cluster used for Vault storage:

✔ Should NOT be reused for other Consul workloads

Why?

✔ Prevent performance interference  
✔ Reduce failure coupling  
✔ Maintain predictable latency  

---

## Example Production Architecture (AWS)

Typical layout:

Vault Nodes → Spread across Availability Zones  
Consul Nodes → Spread across Availability Zones  

Benefits:

✔ Hardware redundancy  
✔ Zone failure tolerance  
✔ Leader failover capability  

---

## Failure Scenario Example

If AZ-1 fails:

✔ Vault cluster survives  
✔ Consul cluster survives  
✔ Leader re-election occurs  

System continues functioning.

---

## Deployment Model Overview

Example:

✔ 3 Vault Nodes  
✔ 5 Consul Server Nodes  

Vault → Uses Consul for storage

---

## Critical Design Pattern — Local Consul Agent

Very important concept.

Vault does NOT directly talk to Consul cluster.

Instead:

Vault → Talks to Local Consul Agent → Agent Talks to Cluster

---

## Why Use Local Consul Agent?

Benefits:

✔ Vault config remains unchanged  
✔ Simplifies node replacement  
✔ Handles cluster topology changes  
✔ Reduces reconfiguration effort  

Vault always connects to:

127.0.0.1:8500

Consistent abstraction layer.

---

## Hands-On Workflow — Step by Step

---

## Part 1 — Deploy Consul Cluster

Perform on Consul Server Nodes.

---

## Step 1 — Install Consul

    curl -Lo consul.zip https://releases.hashicorp.com/consul/<version>/consul_<version>_linux_amd64.zip

    unzip consul.zip
    sudo mv consul /usr/local/bin/

---

## Step 2 — Create Consul Configuration

Example:

    sudo vi /etc/consul.d/consul.hcl

---

## Example Consul Server Config

    server = true
    node_name = "consul-node-1"
    datacenter = "dc1"
    data_dir = "/opt/consul"

    bind_addr = "0.0.0.0"

    retry_join = ["<consul-node-2-ip>", "<consul-node-3-ip>"]

    ui_config {
      enabled = true
    }

---

## Important Parameters

✔ server = true → Voting member  
✔ retry_join → Cluster formation  
✔ data_dir → Persistent storage  

---

## Step 3 — Start Consul

    consul agent -config-dir=/etc/consul.d

Repeat on all Consul nodes.

---

## Step 4 — Verify Consul Cluster

    consul members

---

## Part 2 — Configure Vault Nodes

Perform on Vault Nodes.

---

## Step 1 — Install Consul Client on Vault Nodes

Same Consul binary installation.

---

## Step 2 — Configure Consul Client

    sudo vi /etc/consul.d/consul.hcl

---

## Example Consul Client Config

    server = false
    node_name = "vault-node-1"
    datacenter = "dc1"
    data_dir = "/opt/consul"

    retry_join = ["<consul-server-ip>"]

---

## Why server = false?

✔ Client mode  
✔ No voting rights  
✔ Lightweight agent  

---

## Step 3 — Start Consul Client

    consul agent -config-dir=/etc/consul.d

Vault node now joins Consul cluster.

---

## Part 3 — Configure Vault Storage Backend

---

## Step 1 — Edit Vault Configuration File

    sudo vi /etc/vault.d/vault.hcl

---

## Example Vault Consul Storage Config

    storage "consul" {
      address = "127.0.0.1:8500"
      path    = "vault/"
    }

---

## Why 127.0.0.1?

Vault talks ONLY to local Consul agent.

Agent handles cluster communication.

---

## Step 2 — Configure Listener (Vault)

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

---

## Step 3 — Start Vault

    vault server -config=/etc/vault.d/vault.hcl

---

## Step 4 — Check Vault Status

    vault status

Expected:

Initialized → false  
Sealed → true  

---

## Step 5 — Initialize Vault

    vault operator init

---

## Step 6 — Unseal Vault

    vault operator unseal

Provide required keys.

---

## Step 7 — Verify Vault Operations

Vault now stores data inside:

✔ Consul cluster (via local agent)

---

## Final Mental Model — Vault + Consul Interaction

Vault Flow:

Vault → Local Consul Agent → Consul Cluster Leader → Replication

Vault never cares about:

✔ Consul topology  
✔ Leader changes  
✔ Node failures  

Consul abstracts complexity.

---

## Why This Architecture Is Powerful

✔ Independent scaling  
✔ Strong HA guarantees  
✔ Mature ecosystem  
✔ Clear separation of concerns  

Vault → Security Engine  
Consul → Storage & Coordination Engine

---

## Critical Production Insights

✔ Use dedicated Consul cluster  
✔ Prefer 3 or 5 Consul servers  
✔ Always use local Consul agent  
✔ TLS mandatory in real deployments  
✔ Monitor Consul quorum health  
✔ Snapshot Consul regularly  

---

## Summary

You now understand:

✔ Why Consul is used  
✔ Consul consensus model  
✔ Leader election behavior  
✔ AWS HA architecture reasoning  
✔ Local Consul agent pattern  
✔ Full installation workflow  
✔ Vault configuration  
✔ Operational flow  

