# MySQL Template

MySQL is the world's most popular open-source relational database management system, known for its speed, reliability, and ease of use.

## Details

| Property | Value |
|----------|-------|
| Image | `mysql:{VERSION}` |
| Port | 3306 |
| Category | Database |
| Storage | 10Gi |

## Supported Versions

| Version | Status | Notes |
|---------|--------|-------|
| 8.4 | Latest | Innovation release |
| 8.0 | Default | LTS, recommended for production |
| 5.7 | Legacy | Extended support |
| 5.6 | Legacy | Minimum supported |

## Configuration Variables

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `VERSION` | string | No | `9` | MySQL version to deploy |
| `MYSQL_ROOT_PASSWORD` | secret | Yes | (auto-generated) | MySQL root user password |
| `MYSQL_USER` | string | No | `app` | MySQL application user username |
| `MYSQL_PASSWORD` | secret | Yes | (auto-generated) | MySQL application user password |
| `MYSQL_DATABASE` | string | No | `app` | Default database name to create |

## Version Override

The `VERSION` variable selects the MySQL image version. CLI deployment uses `defaultVersion`; select another version in the Infrasutra UI or change the default in a customized `infra-template.yaml` before syncing:

```yaml
defaultVersion: "8"
```

Then deploy the published template:

```bash
infra templates deploy mysql
```

## Storage

This template provisions a 10Gi persistent volume mounted at `/var/lib/mysql` for database storage.

## Health Check

The template uses a TCP health check on port 3306 to ensure the database is ready to accept connections.

## Resource Defaults

| Resource | Value |
|----------|-------|
| CPU | 100m |
| Memory | 512Mi |
| Replicas | 1 |

## Connection

Once deployed, connect to MySQL using:

```bash
mysql -h <service-host> -P 3306 -u $MYSQL_USER -p$MYSQL_PASSWORD $MYSQL_DATABASE
```

Or as root:

```bash
mysql -h <service-host> -P 3306 -u root -p$MYSQL_ROOT_PASSWORD
```

Connection string format:
```
mysql://$MYSQL_USER:$MYSQL_PASSWORD@<service-host>:3306/$MYSQL_DATABASE
```

## External Access

By default, MySQL is only accessible within the cluster. In a customized `infra-template.yaml`, set `tcp_exposure.enabled` to `true`, sync the template, and deploy it:

```yaml
tcp_exposure:
  enabled: true
```

```bash
infra templates deploy mysql
```

When enabled, the service will be accessible at `{name}-{stage}.{domain}:3306` with TLS termination at the gateway. The gateway handles TLS encryption and forwards plain TCP to MySQL.

## Notes

- Both root and application user passwords are auto-generated and stored as Kubernetes secrets
- Data persists across restarts using the provisioned volume
- The application user has full privileges on the specified database
- All versions use the same configuration, only the image tag changes
- External access is disabled by default for security
