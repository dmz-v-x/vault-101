## Configuring Integrated Storage Backend (Raft)

## Prerequisite Mental Model — What Is Integrated Storage?

Traditional Vault architecture:

Vault Nodes → External Storage (Consul)

Integrated Storage architecture:

Vault Nodes → Store Data Themselves → Replicated via Raft

Meaning:

✔ Vault becomes self-contained  
✔ No external storage dependency  
✔ Built-in replication & consensus  

---

## Why Integrated Storage Exists

Integrated Storage solves major operational problems:

✔ No Consul cluster required  
✔ No network hop to storage  
✔ Simpler troubleshooting  
✔ Fewer moving parts  
✔ Native HA support  

This is why most modern Vault deployments use Raft.

---

## Core Characteristics of Integrated Storage

Integrated Storage provides:

✔ Vault internal storage  
✔ High Availability  
✔ Raft consensus protocol  
✔ Data replication across nodes  
✔ Built-in snapshots  
✔ HashiCorp supported  

---

## Key Advantage — Only Troubleshoot Vault

With Consul backend:

✔ Troubleshoot Vault + Consul

With Raft backend:

✔ Troubleshoot Vault only

This is a huge operational benefit.

---

## Critical Raft Mental Model

Raft protocol ensures:

✔ Consistency  
✔ Leader election  
✔ Replication  
✔ Fault tolerance  

Within a Vault cluster:

✔ One leader (active node)  
✔ Other followers (standby nodes)

---

## Cluster Architecture Overview

Example cluster:

Vault Node A → Active  
Vault Node B → Standby  
Vault Node C → Standby  

All nodes:

✔ Store Vault data locally  
✔ Replicate data via Raft  

---

## Step 1 — Configure Storage Stanza on Each Node

Each Vault node requires its own configuration file.

Example:

    storage "raft" {
      path    = "/opt/vault/data"
      node_id = "node-a"
    }

---

### Parameters Explained

#### path

Local directory where Raft data is stored.

Each node has:

✔ Its own storage directory

---

#### node_id

Unique identifier for the node.

Must be:

✔ Unique across cluster

---

## Step 2 — Configure Listener (Required)

Example:

    listener "tcp" {
      address         = "0.0.0.0:8200"
      cluster_address = "0.0.0.0:8201"

      tls_cert_file   = "/etc/vault/tls/server.crt"
      tls_key_file    = "/etc/vault/tls/server.key"
    }

---

## Why cluster_address Matters

Used for:

✔ Raft replication traffic  
✔ Node-to-node communication  

---

## Step 3 — Start Vault on All Nodes

On each node:

    vault server -config=/etc/vault.d/vault.hcl

At this stage:

✔ Nodes are independent  
✔ No cluster formed yet  

---

## Step 4 — Initialize ONLY the First Node

Pick ONE node (future active node).

Run:

    vault operator init

Vault generates:

✔ Master key  
✔ Unseal keys / Recovery keys  
✔ Root token  

---

## Important Rule — Initialize Once Per Cluster

Initialization defines:

✔ Cluster cryptographic identity

Never initialize multiple nodes separately.

---

## Step 5 — Unseal First Node

    vault operator unseal

Provide required keys.

Vault becomes:

✔ Active node  

---

## Step 6 — Join Standby Nodes to Cluster

Now we connect other nodes.

Run on standby node:

    vault operator raft join https://active_node.example.com:8200

Example:

    vault operator raft join https://node-a:8200

---

## What Happens Internally During Join

Standby node:

1. Contacts active node  
2. Requests cluster membership  
3. Receives Raft configuration  
4. Starts replication  

Node becomes follower.

---

## Step 7 — Unseal Standby Node

Even after join:

✔ Node remains sealed

Run:

    vault operator unseal

Provide required keys.

Now node becomes:

✔ Standby follower  

---

## Step 8 — Repeat for Additional Nodes

Repeat:

✔ Join command  
✔ Unseal command  

For Node C, Node D, etc.

---

## Step 9 — Verify Cluster Members

Run:

    vault operator raft list-peers

---

## Example Output

    Node       Address            State
    ----       -------            -----
    node-a     https://node-a     leader
    node-b     https://node-b     follower
    node-c     https://node-c     follower

---

## What This Output Means

✔ Leader → Active Vault Node  
✔ Followers → Standby Nodes  

All nodes:

✔ Replicating data  
✔ Fully synchronized  

---

## Critical Concept — Data Replication

Integrated Storage ensures:

✔ Every node stores Vault data  
✔ Data replicated automatically  
✔ Strong consistency guarantees  

If leader fails:

✔ Followers elect new leader

---

## Built-in Snapshots (Major Advantage)

Raft provides:

✔ Native snapshot capability

Used for:

✔ Backups  
✔ Disaster recovery  
✔ Data retention  

Example:

    vault operator raft snapshot save backup.snap

---

## Why Snapshots Matter

✔ No external backup tooling required  
✔ Consistent state capture  

---

## Major Advantage — Eliminates Consul Network Hop

With Consul:

Vault → Network → Consul → Network → Vault

With Raft:

Vault → Local Disk

✔ Lower latency  
✔ Better performance  
✔ Fewer failure points  

---

## Failure Scenario Mental Model

If Standby Node Fails:

✔ No data loss  

If Leader Fails:

✔ Automatic leader election  

If Multiple Nodes Fail:

✔ Cluster survives if quorum maintained  

---

## What Is Quorum?

Minimum nodes required for Raft consensus.

Example:

3-node cluster → Quorum = 2

Lose 2 nodes → Cluster unavailable.

---

## Critical Production Best Practices

✔ Minimum 3 nodes  
✔ Prefer 5 nodes for large systems  
✔ Unique node_id per node  
✔ TLS mandatory  
✔ Regular snapshots  
✔ Monitor quorum health  

---

## Final Mental Model — Integrated Storage

Integrated Storage = Vault Native Distributed Database

Vault Nodes:

✔ Store data locally  
✔ Replicate via Raft  
✔ Elect leader  
✔ Maintain consistency  

Architecture becomes:

✔ Simpler  
✔ Faster  
✔ Easier to operate  

---

## Summary

You now understand:

✔ What integrated storage is  
✔ Why Raft exists  
✔ How Vault nodes replicate data  
✔ Initialization rules  
✔ Cluster joining workflow  
✔ Leader vs follower behavior  
✔ list-peers command  
✔ Snapshot capability  
✔ Failure scenarios  
✔ Quorum concept  
