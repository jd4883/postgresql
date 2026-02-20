# PostgreSQL (CloudNativePG 3-node cluster)

Helm chart that creates a **CloudNativePG** `Cluster` (3 instances) in namespace **postgresql**, with superuser credentials from **1Password** via External Secrets. The **postgresql-operator** chart (operator/) in this repo must be installed first (e.g. in cluster-tools).

---

## Database hostname for 1Password and apps

Use this hostname when configuring apps (Nextcloud, Immich, etc.) and when documenting credentials in 1Password:

**`postgresql-rw.postgresql.svc.cluster.local`**

- **Port:** 5432  
- **Namespace:** postgresql  

Create **one database per app** (e.g. `nextcloud`, `immich`) and a dedicated user with access to that database. Store each app's credentials in 1Password (e.g. nextcloud-db, immich-db) and use External Secrets in the app charts to sync them.

---

## Requirements

| Dependency | Notes |
|------------|--------|
| **CloudNativePG operator** | Install the **postgresql-operator** chart (operator/) in cluster-tools first. |
| **1Password item** | Item (e.g. **postgresql**) with property **password** (superuser postgres password). Used for cluster bootstrap. |
| **Namespace** | **postgresql** (create if needed). |
| **External Secrets Operator** | ClusterSecretStore (e.g. onepassword-connect). |
| **Nodes** | Soft affinity to nodes with label **node-role=database** (homelab convention). |
| **Storage** | Dynamic provisioning via **Longhorn** (storageClass: longhorn, 25Gi per instance). PDB: operator allows one pod down at a time. |

---

## 1Password items to add

1. **postgresql** — For cluster bootstrap. Add field **password** (superuser `postgres` password).  
2. **nextcloud** (or your DB item for Nextcloud) — Fields **username** and **password** for the nextcloud DB user (syncs to Secret `nextcloud-db` in the nextcloud chart).  
3. **immich-db-credentials** — Fields **DB_USERNAME** and **DB_PASSWORD** for Immich (used by immich chart).  

After the cluster is running, create databases and users (e.g. `CREATE DATABASE nextcloud; CREATE USER nextcloud WITH PASSWORD '...'; GRANT ALL ON DATABASE nextcloud TO nextcloud;` and similarly for immich). Use the same credentials you store in 1Password for each app.

---

## Chart contents

- **ExternalSecret** — Syncs superuser password from 1Password into Secret `postgresql-superuser` (type `kubernetes.io/basic-auth`).  
- **Cluster** — CloudNativePG CR: 3 instances, bootstrap from that secret, storage size from values.

---

## Render & validation

> `helm template postgresql . -f values.yaml -n postgresql`

---

## Next steps

1. Install **postgresql-operator** (operator/) in cluster-tools.  
2. Create 1Password item **postgresql** with **password**.  
3. Install this chart in namespace **postgresql**.  
4. When the cluster is up, create databases/users for nextcloud, immich, etc., and add those credentials to 1Password.
