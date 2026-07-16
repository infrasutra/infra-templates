# PostgreSQL Template

PostgreSQL is a powerful, open-source object-relational database system with a strong reputation for reliability, feature robustness, and performance.

## Details

| Property | Value |
|----------|-------|
| Image | `postgres:{VERSION}-alpine` |
| Port | 5432 |
| Category | Database |
| Storage | 10Gi |

## Supported Versions

| Version | Status | Notes |
|---------|--------|-------|
| 18 | Latest | Newest major release |
| 17 | Stable | Current stable |
| 16 | Default | LTS, recommended for production |
| 15 | Stable | Previous stable |
| 14 | Stable | Extended support |
| 13 | Legacy | Minimum supported |

## Configuration Variables

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `VERSION` | string | No | `16` | PostgreSQL version to deploy |
| `EXPOSE_EXTERNAL` | boolean | No | `false` | Enable external access via TLSRoute |
| `POSTGRES_USER` | string | No | `postgres` | PostgreSQL superuser username |
| `POSTGRES_PASSWORD` | secret | Yes | (auto-generated) | PostgreSQL superuser password |
| `POSTGRES_DB` | string | No | `postgres` | Default database name to create |

## Version Override

The `VERSION` variable allows you to select which PostgreSQL version to deploy. You can override it at deploy time:

```bash
infra deploy --set VERSION=17
```

Or in your `infra.yaml`:

```yaml
variables:
  - key: VERSION
    default: "17"
```

## Storage

This template provisions a 10Gi persistent volume mounted at `/var/lib/postgresql/data` for database storage.

## Health Check

The template uses a TCP health check on port 5432 to ensure the database is ready to accept connections.

## Resource Defaults

| Resource | Value |
|----------|-------|
| CPU | 100m |
| Memory | 256Mi |
| Replicas | 1 |

## Connection

Once deployed, connect to PostgreSQL using:

```bash
psql -h <service-host> -p 5432 -U $POSTGRES_USER -d $POSTGRES_DB
```

Connection string format:
```
postgresql://$POSTGRES_USER:$POSTGRES_PASSWORD@<service-host>:5432/$POSTGRES_DB
```

## External Access

By default, PostgreSQL is only accessible within the cluster (internal only). To enable external access via TLSRoute:

```bash
infra deploy --template postgres --set EXPOSE_EXTERNAL=true
```

When enabled, the service will be accessible at `{name}-{stage}.{domain}:5432` with TLS termination at the gateway. The gateway handles TLS encryption and forwards plain TCP to PostgreSQL.

## Notes

- Password is auto-generated and stored as a Kubernetes secret
- Data persists across restarts using the provisioned volume
- Alpine-based image for smaller footprint
- All versions use the same configuration, only the image tag changes
- External access is disabled by default for security
