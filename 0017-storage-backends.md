## Storage Backend

## Prerequisite Mental Model — What Problem Does Storage Backend Solve?

Vault is NOT a database.

Vault is:

✔ A security system  
✔ A secrets management engine  
✔ A cryptographic barrier  

Vault still needs a place to persist:

• Encrypted secrets  
• Tokens  
• Policies  
• Metadata  
• Lease information  
• Internal state  

That place is:

**Storage Backend**

Think of it as:

Vault Brain → Storage Backend = Persistent Memory

---

## What Is a Storage Backend?

A Storage Backend is:

The system where Vault stores ALL of its data in encrypted form.

Important clarification:

Vault ALWAYS encrypts data before writing to storage.

Storage backend NEVER sees plaintext secrets.

---

## Core Responsibilities of Storage Backend

Storage backend stores:

✔ Encrypted secrets  
✔ Encrypted encryption keys  
✔ Policies  
✔ Tokens  
✔ Identity data  
✔ Lease metadata  
✔ Cluster coordination data  

Without storage → Vault cannot function.

---

## Key Rule — One Storage Backend Per Vault Cluster

This is extremely important.

Vault design principle:

✔ One cluster → One storage backend  
✔ NOT one storage backend per node  

---

## Why Only One Storage Backend?

Vault nodes must share:

✔ Same data  
✔ Same tokens  
✔ Same policies  
✔ Same encryption structures  

Multiple storage backends would break consistency.

---

## Single Node Vault Behavior

Scenario:

You deploy:

✔ One Vault Node

Vault interacts with:

✔ One Storage Backend

All data lives there.

Simple model.

---

## Multi-Node Vault Cluster Behavior

Scenario:

You deploy:

✔ Multiple Vault Nodes (HA Cluster)

Behavior:

✔ ALL nodes interact with SAME storage backend

Meaning:

Vault Cluster = Logical System  
Storage Backend = Shared Persistent State

---

## High Availability (HA) and Storage Backend

When HA is enabled:

✔ Only ONE active node exists  
✔ Other nodes remain standby  

Standby nodes:

✔ Still connected to storage  
✔ Still synchronized  
✔ Ready to become active  

---

## Why Storage Backend Matters for HA

HA-capable backends provide:

✔ Locking mechanisms  
✔ Leader election support  
✔ Coordination primitives  

Without HA support → Multi-node cluster unstable.

---

## Replication vs Storage Backend (Critical Concept)

Beginners often misunderstand this.

Important rule:

✔ Replication does NOT happen between storage backends

Instead:

✔ Vault Nodes replicate via APIs

---

## How Replication Actually Works (Enterprise)

Scenario:

You deploy:

✔ Cluster A → Storage Backend A  
✔ Cluster B → Storage Backend B  

Clusters DO NOT share storage.

Vault performs replication:

✔ Node-to-node via APIs

Storage remains isolated per cluster.

---

## Why This Design Exists

Benefits:

✔ Independent failure domains  
✔ Flexible storage choices  
✔ Strong security isolation  
✔ Clean disaster recovery models  

---

## Multiple Clusters = Multiple Storage Backends

Key rule:

✔ Each Vault Cluster → Own Storage Backend

Clusters NEVER share storage.

---

## Can Different Clusters Use Different Storage Backends?

Yes.

Example:

Cluster A → Consul  
Cluster B → Raft  

Perfectly valid.

---

## HashiCorp Supported vs Community Supported Backends

Important production guidance.

---

## Enterprise Vault Recommendation

For Enterprise:

✔ HashiCorp Consul  
✔ Integrated Storage (Raft)

Everything else:

✔ Community Supported

---

## Why HashiCorp-Supported Backends Are Preferred

✔ Official support  
✔ Better HA guarantees  
✔ Better reliability  
✔ Better operational tooling  

Critical for production systems.

---

## Choosing a Storage Backend — Logical Decision Flow

Instead of memorizing backend lists, think logically.

---

## Scenario 1 — Learning / Development

Use:

✔ In-Memory Storage (Dev Mode)

Why?

✔ No persistence required  
✔ Simplest setup  
✔ Fast experimentation  

Limitation:

✔ Data lost on restart

---

## Scenario 2 — Production Without HA

Use:

✔ Filesystem Backend (HashiCorp Supported)

Example:

storage "file"

Why?

✔ Simple  
✔ Stable  
✔ Minimal infrastructure  

Limitation:

✔ No native HA support

---

## Scenario 3 — Production With HA (Most Common)

Use:

✔ Consul (HashiCorp Supported)  
✔ Raft Integrated Storage (HashiCorp Supported)

Why?

✔ HA support  
✔ Leader election  
✔ Locking primitives  

---

## Scenario 4 — Specialized Environments

Community Supported Backends:

✔ DynamoDB  
✔ PostgreSQL  
✔ MySQL  
✔ Etcd  
✔ Cassandra  
✔ Cloud-native storage  

Use only when:

✔ Specific architectural needs exist  
✔ Operational expertise available  

---

## Practical Backend Categories

Simplify complexity by grouping.

---

## Backends Without HA Support

Examples:

✔ FileSystem  
✔ Some community backends  

Characteristics:

✔ Suitable for single-node Vault  
✔ Simple deployments  

---

## Backends With HA Support

Examples:

✔ Consul  
✔ Raft  
✔ DynamoDB  
✔ PostgreSQL  

Characteristics:

✔ Multi-node clusters  
✔ Leader election  
✔ Locking mechanisms  

---

## Configuring Storage Backend in Vault Configuration File

Storage backend is defined using:

storage stanza

---

## Example — Consul Backend

    storage "consul" {
      address = "127.0.0.1:8500"
      path    = "vault/"
      token   = "09s8dfjn23r09sdufjn23"
    }

---

### Parameters Explained

#### address

Where Consul agent is running.

---

#### path

Logical storage prefix inside Consul.

---

#### token

Authentication token for Consul access.

---

## Example — Raft Integrated Storage

    storage "raft" {
      path    = "/opt/vault/data"
      node_id = "node-a-us-east1.example.com"

      retry_join {
        auto_join = "provider=aws region=us-east-1 tag_key=vault tag_value=us-east-1"
      }
    }

---

### Why Raft Is Extremely Important

Raft provides:

✔ Built-in storage  
✔ Built-in HA  
✔ No external dependencies  
✔ Simpler architecture  

This is why modern Vault deployments prefer Raft.

---

### Parameters Explained

#### path

Where Raft data lives.

---

#### node_id

Unique cluster node identifier.

---

#### retry_join

Cluster auto-discovery mechanism.

Used for:

✔ HA clusters  
✔ Auto-scaling environments  

---

## Why Storage Backend Choice Is a Critical Decision

Storage backend affects:

✔ Availability  
✔ Performance  
✔ Failure recovery  
✔ Scalability  
✔ Operational complexity  
✔ Disaster recovery  

Wrong choice → Expensive redesign later.

---

## Critical Production Insights

✔ Use HashiCorp-supported backends for production  
✔ Raft is the modern default choice  
✔ Consul still widely used in large enterprises  
✔ Filesystem only for non-HA setups  
✔ Community backends require expertise  

---

## Final Mental Model

Vault Cluster → One Storage Backend  
Vault Nodes → Share Storage Backend  
Multiple Clusters → Separate Storage Backends  
Replication → Happens Between Vault Nodes (NOT Storage)

Storage Backend = Persistence + Coordination + Stability

---

## Summary

You now understand:

✔ What storage backend is  
✔ Why Vault needs it  
✔ One backend per cluster rule  
✔ Single-node vs multi-node behavior  
✔ HA implications  
✔ Replication interaction  
✔ Backend selection logic  
✔ Configuration examples  


