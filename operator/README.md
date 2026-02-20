# postgresql-operator

Helm chart that installs the **CloudNativePG** operator in the **cluster-tools** namespace. The operator manages PostgreSQL clusters via the `Cluster` CRD. The actual 3-node database is deployed by the **postgresql** chart in this repo (**cluster/**) in namespace **postgresql**.

---

## Chart contents

- **CloudNativePG operator** (subchart) — watches for `Cluster` CRs and reconciles PostgreSQL instances.

---

## Requirements

| Dependency | Notes |
|------------|--------|
| Namespace | **cluster-tools** (create if needed). |
| Argo CD | Typically one Application for this chart in project cluster-tools. |

---

## Render & validation

> `helm dependency update . && helm template postgresql-operator . -f values.yaml -n cluster-tools`

---

## Next steps

1. Install the **postgresql** chart (cluster/) in namespace **postgresql** to create the 3-node cluster and credentials from 1Password.
2. Database hostname for apps: **`postgresql-rw.postgresql.svc.cluster.local`** (add to 1Password items for nextcloud, immich, etc.).
