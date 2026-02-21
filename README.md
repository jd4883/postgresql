# PostgreSQL (homelab)

One repo, **two Helm charts** for PostgreSQL on Kubernetes:

| Chart | Path | Purpose |
|-------|------|---------|
| **postgresql-operator** | `operator/` | CloudNativePG operator — install in **cluster-tools**. Manages `Cluster` CRDs. |
| **postgresql** | `cluster/` | 3-node PostgreSQL cluster — install in **postgresql** namespace. Credentials from 1Password; hostname `postgresql-rw.postgresql.svc.cluster.local`. |

---

## Install order

1. **operator:** `helm install postgresql-operator ./operator -n cluster-tools -f operator/values.yaml`
2. **cluster:** `helm install postgresql ./cluster -n postgresql -f cluster/values.yaml`

Argo CD: use two Applications (or one app per path), e.g. path `operator` and path `cluster` with the appropriate namespaces.

---

## Requirements

- **operator:** namespace `cluster-tools`.
- **cluster:** namespace `postgresql`, 1Password item for superuser password, External Secrets Operator, nodes with label `node-role=database` (soft affinity).

See **operator/README.md** and **cluster/README.md** for per-chart details.
