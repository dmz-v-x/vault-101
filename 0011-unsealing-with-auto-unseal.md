## Unsealing with Auto Unseal 

## Prerequisite Mental Model — What Changes with Auto Unseal?

In the default Shamir model:

Master Key → Split into Unseal Key Shards

With Auto Unseal:

Master Key → Encrypted by AWS KMS Key → Stored Securely

Meaning:

• No manual unseal keys required for startup  
• Vault unseals automatically  
• Cloud KMS protects master key  

Important clarification:

Vault will still generate keys during initialization.

But they are now:

• Recovery Keys (NOT Unseal Keys)

This distinction is critical.

---

## Part 1 — Basic Requirements

Before starting, ensure you have:

• AWS Account  
• Permission to manage KMS & IAM  
• Linux Server (EC2 recommended)  
• Vault Binary Installed  

Optional (recommended for production):

• TLS Certificates

---

## Part 2 — Create AWS KMS Key

Vault will use this key to protect its **master key**.

---

## Step 1 — Open AWS KMS Console

Navigate to:

AWS Console → Key Management Service (KMS)

Click:

Create Key

---

## Step 2 — Key Configuration

Choose:

• Symmetric Key  
• Key Usage → Encrypt and Decrypt  
• Key Material Origin → AWS KMS  

Why symmetric?

Vault’s seal mechanism requires symmetric encryption.

---

## Step 3 — Assign Alias

Example:

vault-unseal-key

Alias improves readability.

---

## Step 4 — Configure Permissions

Grant usage permissions to:

• Vault EC2 IAM Role

Vault needs:

• Encrypt  
• Decrypt  
• DescribeKey  

---

## Step 5 — Save Key ARN

Example:

arn:aws:kms:us-east-1:123456789012:key/xxxx

You will need this in Vault configuration.

---

## Part 3 — Create IAM Policy for Vault

Vault must be allowed to use the KMS key.

---

## Example IAM Policy

    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "AllowUseOfKMSKey",
          "Effect": "Allow",
          "Action": [
            "kms:Encrypt",
            "kms:Decrypt",
            "kms:DescribeKey"
          ],
          "Resource": "arn:aws:kms:region:account-id:key/your-key-id"
        }
      ]
    }

Replace:

• Resource → Your KMS Key ARN

---

## Why These Permissions?

kms:Encrypt → Protect master key  
kms:Decrypt → Retrieve master key  
kms:DescribeKey → Validate key metadata  

---

## Part 4 — Create IAM Role for Vault Server

---

## Step 1 — Create Role

IAM → Roles → Create Role

Trusted Entity:

• EC2

---

## Step 2 — Attach Policy

Attach:

VaultKMSAccess

---

## Step 3 — Assign Role to EC2

Launch EC2 → Select VaultServerRole

Vault now inherits permissions automatically.

---

## Part 5 — Install Vault on Linux

Example (Ubuntu/Debian):

    curl -fsSL https://apt.releases.hashicorp.com/gpg \
      | sudo gpg --dearmor \
      -o /etc/apt/trusted.gpg.d/hashicorp.gpg

    sudo apt-add-repository \
    "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"

    sudo apt update
    sudo apt install vault -y

---

## What This Does

• Adds HashiCorp repository  
• Installs Vault binary  
• Installs CLI  

---

## Part 6 — Create Vault System User

    sudo useradd --system --home /etc/vault.d --shell /bin/false vault

Why?

Vault should never run as root.

---

## Part 7 — Create Storage Directory

    sudo mkdir -p /opt/vault/data
    sudo chown -R vault:vault /opt/vault

Vault needs persistent storage.

---

## Part 8 — Vault Configuration File

Create:

/etc/vault.d/vault.hcl

---

## Example Configuration

    storage "file" {
      path = "/opt/vault/data"
    }

    listener "tcp" {
      address     = "0.0.0.0:8200"
      tls_disable = "true"
    }

    seal "awskms" {
      region     = "us-east-1"
      kms_key_id = "arn:aws:kms:us-east-1:123456789012:key/your-key-id"
    }

    api_addr     = "http://<server-ip>:8200"
    cluster_addr = "http://<server-ip>:8201"

    ui = true

---

## Most Important Section — seal "awskms"

This tells Vault:

Use AWS KMS to protect master key.

Vault will:

• Encrypt master key using KMS  
• Store encrypted master key  
• Retrieve automatically  

---

## Part 9 — Create Systemd Service

Create:

/etc/systemd/system/vault.service

---

## Example Service

    [Unit]
    Description=Vault
    Requires=network-online.target
    After=network-online.target

    [Service]
    User=vault
    Group=vault
    ExecStart=/usr/bin/vault server -config=/etc/vault.d/vault.hcl
    ExecReload=/bin/kill --signal HUP $MAINPID
    KillMode=process
    Restart=on-failure
    LimitNOFILE=65536

    [Install]
    WantedBy=multi-user.target

---

## Part 10 — Start Vault

    sudo systemctl daemon-reexec
    sudo systemctl enable vault
    sudo systemctl start vault

---

## Part 11 — Set Vault Address

    export VAULT_ADDR='http://127.0.0.1:8200'

CLI now knows Vault location.

---

## Part 12 — Vault Startup Behavior (Auto Unseal Magic)

Vault starts → Immediately sealed internally

Vault then:

1. Reads encrypted master key from storage
2. Calls AWS KMS
3. KMS decrypts master key
4. Vault decrypts encryption key
5. Vault becomes unsealed

No human intervention required.

---

## Part 13 — Initialize Vault

    vault operator init

Vault generates:

• Recovery Keys  
• Root Token  

---

## Recovery Keys vs Unseal Keys

With Auto Unseal:

• Recovery Keys → Emergency operations  
• AWS KMS → Unsealing mechanism  

Vault unseals using KMS, not keys.

---

## Part 14 — Login to Vault

    vault login <root-token>

---

## Part 15 — Access Vault UI (Optional)

Open:

http://<server-ip>:8200/ui

Vault should already be unsealed.

---

## Final Mental Model — Auto Unseal Workflow

Without Auto Unseal:

Humans → Reconstruct Master Key

With Auto Unseal:

AWS KMS → Protects Master Key

Vault Startup:

Vault → Storage → KMS → Master Key → Encryption Key → Data

---

## Why Auto Unseal Is Preferred

• No manual unseal operations  
• Cloud-friendly  
• HA-friendly  
• Kubernetes-friendly  
• Lower operational burden  

---

## Critical Security Responsibilities Still Exist

Even with Auto Unseal:

• Protect KMS Key  
• Protect IAM Role  
• Protect Recovery Keys  
• Protect Root Token  

Compromised KMS = Compromised Vault


