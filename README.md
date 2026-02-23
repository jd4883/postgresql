# PostgreSQL (homelab)

One repo, **three Helm charts** for PostgreSQL on Kubernetes:

| Chart | Path | Purpose |
|-------|------|---------|
| **postgresql-operator** | `operator/` | CloudNativePG operator — install in **cluster-tools**. Manages `Cluster` CRDs. |
| **postgresql** | `cluster/` | 3-node PostgreSQL cluster — install in **postgresql** namespace. Credentials from 1Password; hostname `postgresql-rw.postgresql.svc.cluster.local`. |
| **postgresql-backup-to-minio** | `backup-to-minio/` | CronJob: pg_dumpall → MinIO (HDD/backups tier). Install in **postgresql** namespace. |

---

## Install order

1. **operator:** `helm install postgresql-operator ./operator -n cluster-tools -f operator/values.yaml`
2. **cluster:** `helm install postgresql ./cluster -n postgresql -f cluster/values.yaml`
3. **backup-to-minio:** `helm install postgresql-backup-to-minio ./backup-to-minio -n postgresql -f backup-to-minio/values.yaml`

Argo CD: use Applications with paths `operator`, `cluster`, and `backup-to-minio` (sourceRepo: homelab-postgresql).

---

## Requirements

- **operator:** namespace `cluster-tools`.
- **cluster:** namespace `postgresql`, 1Password item for superuser password, External Secrets Operator, nodes with label `node-role=database` (soft affinity).

See **operator/README.md** and **cluster/README.md** for per-chart details.
