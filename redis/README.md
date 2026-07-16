# Redis Template

Redis is an open-source, in-memory data structure store used as a database, cache, message broker, and streaming engine.

## Details

| Property | Value |
|----------|-------|
| Image | `redis:{VERSION}-alpine` |
| Port | 6379 |
| Category | Cache |
| Storage | 5Gi |

## Supported Versions

| Version | Status | Notes |
|---------|--------|-------|
| 8 | Latest | Newest major release |
| 7 | Default | Recommended for production |
| 6 | Legacy | Previous stable |

## Configuration Variables

| Variable | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `VERSION` | string | No | `7` | Redis version to deploy |
| `EXPOSE_EXTERNAL` | boolean | No | `false` | Enable external access via TLSRoute |
| `REDIS_PASSWORD` | secret | Yes | (auto-generated) | Redis authentication password |

## Version Override

The `VERSION` variable allows you to select which Redis version to deploy. You can override it at deploy time:

```bash
infra deploy --set VERSION=8
```

Or in your `infra.yaml`:

```yaml
variables:
  - key: VERSION
    default: "8"
```

## Storage

This template provisions a 5Gi persistent volume mounted at `/data` for Redis data persistence. AOF (Append Only File) persistence is enabled by default.

## Persistence

The template configures Redis with `--appendonly yes` for AOF persistence, ensuring data survives restarts.

## Health Check

The template uses a TCP health check on port 6379 to ensure Redis is ready to accept connections.

## Resource Defaults

| Resource | Value |
|----------|-------|
| CPU | 50m |
| Memory | 128Mi |
| Replicas | 1 |

## Connection

Once deployed, connect to Redis using:

```bash
redis-cli -h <service-host> -p 6379 -a $REDIS_PASSWORD
```

Connection string format:
```
redis://:$REDIS_PASSWORD@<service-host>:6379
```

## External Access

By default, Redis is only accessible within the cluster (internal only). To enable external access via TLSRoute:

```bash
infra deploy --template redis --set EXPOSE_EXTERNAL=true
```

When enabled, the service will be accessible at `{name}-{stage}.{domain}:6379` with TLS termination at the gateway. The gateway handles TLS encryption and forwards plain TCP to Redis.

## Notes

- Password authentication is enabled by default
- Password is auto-generated and stored as a Kubernetes secret
- AOF persistence ensures data durability
- Alpine-based image for smaller footprint
- Suitable for caching, session storage, and message queuing
- External access is disabled by default for security
