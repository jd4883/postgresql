# postgresql-backup-to-minio

CronJob that runs **pg_dumpall** against the shared PostgreSQL cluster and uploads the dump to **MinIO (HDD/backups tier)**. Deploy in namespace **postgresql**.

---

## Chart contents

- **CronJob:** Runs daily (default 02:00); uses `postgres:16-alpine`, installs MinIO client (`mc`), runs pg_dumpall and uploads to MinIO backups tier.
- **Secrets:** Uses existing Secret for Postgres superuser password and a Secret in `postgresql` namespace for MinIO credentials.

---

## Requirements

| Dependency | Notes |
|------------|--------|
| **PostgreSQL** | Shared cluster in namespace `postgresql`; Secret **postgresql-superuser** with key **password**. |
| **MinIO backups tier** | Endpoint e.g. `https://s3-backups.expectedbehaviors.com`. |
| **Secret minio-backups-credentials** | In namespace **postgresql**, with keys **rootUser** and **rootPassword**. |

---

## Install

```bash
helm install postgresql-backup-to-minio ./backup-to-minio -n postgresql -f backup-to-minio/values.yaml
```

Argo CD path: `backup-to-minio` (sourceRepo: homelab-postgresql).
