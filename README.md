# Infrasutra Templates

Official default templates for the Infrasutra platform. These templates provide pre-configured infrastructure services that can be deployed through Infrasutra.

## Available Templates

| Template | Description | Versions | Default |
|----------|-------------|----------|---------|
| [PostgreSQL](./postgres/) | PostgreSQL database with Alpine Linux | 14, 15, 16, 17 | 16 |
| [Redis](./redis/) | Redis in-memory data store with AOF persistence | 6.2, 7.2, 7.4 | 7.2 |
| [MySQL](./mysql/) | MySQL relational database | 5.7, 8.0, 8.4 | 8.0 |

## Usage

Templates can be registered in Infrasutra and deployed to any stage within your projects. Each template follows the Infrasutra YAML specification v1.

### Deploying a Template

```bash
# Deploy with default version
infra deploy --template postgres

# Deploy with specific version
infra deploy --template postgres --version 17

# Deploy with custom configuration
infra deploy --template postgres --set POSTGRES_DB=myapp --set MEMORY=512
```

### Version Selection

All templates support version selection via the `--version` flag or through the Infrasutra UI. The version is interpolated into the container image tag using `${VERSION}`.

### Template Structure

Each template directory contains:
- `infra-template.yaml` - The Infrasutra template specification file
- `README.md` - Documentation for the template including configuration options

## Template Specification

All templates follow the Infrasutra YAML v1 specification:

```yaml
version: "1"
kind: template

# Template metadata
name: template-name
description: Template description
icon: https://example.com/icon.png
category: database
tags:
  - tag1
  - tag2

# Version support
versions:
  - "8"
  - "7"
  - "6"
defaultVersion: "7"

# Variables
variables:
  - key: MY_VAR
    type: string
    description: Variable description
    default: "value"
    configurable: true
  - key: MY_SECRET
    type: secret
    generator: password

# Service definitions
services:
  - name: service-name
    type: statefulset
    image: "image:${VERSION}"
    ports:
      - port: 1234
    env:
      - key: MY_VAR
      - key: MY_SECRET
    volumes:
      - name: data
        path: /data
        size: 10240
    health_check:
      type: exec
      command: ["health-check-command"]
      initial_delay: 10
      interval: 10
      timeout: 5
      retries: 3
    resources:
      cpu: 100
      memory: 256
```

## Key Concepts

### VERSION Variable

The `VERSION` variable is automatically available based on the `versions` array:

- Defined in `versions` array at the spec level
- Default specified by `defaultVersion`
- Can be overridden at deploy time via `--version`
- Used in image tags via `${VERSION}` interpolation

### Variable Types

| Type | Description |
|------|-------------|
| `string` | Plain text value |
| `secret` | Sensitive value stored encrypted |
| `integer` | Numeric value |
| `boolean` | True/false value |

### Generators

| Generator | Description |
|-----------|-------------|
| `password` | Generate a random password |
| `secret` | Generate a random secret string |
| `uuid` | Generate a UUID |

### Service Types

| Type | Description |
|------|-------------|
| `statefulset` | StatefulSet with persistent storage (databases, caches) |
| `deployment` | Deployment for stateless workloads |
| `cronjob` | CronJob for scheduled tasks |

### Resources

All templates include configurable resource allocations:

| Variable | Description | Unit |
|----------|-------------|------|
| `CPU` | CPU allocation | millicores |
| `MEMORY` | Memory allocation | MB |
| `STORAGE_SIZE` | Persistent volume size | MB |

### TCP Exposure (External Access)

Database and cache templates support optional external access via Gateway API TLSRoute. By default, services are internal-only for security.

```yaml
tcp_exposure:
  enabled: ${EXPOSE_EXTERNAL}
  hostname: "${NAME}-${STAGE}.${DOMAIN}"
  port: 5432
  tls:
    mode: terminate
  gateway:
    name: dms-gateway
    namespace: dms-system
```

**TLS Modes:**

| Mode | Description | Use Case |
|------|-------------|----------|
| `terminate` | Gateway terminates TLS, forwards plain TCP | Standard databases without built-in TLS |
| `passthrough` | Gateway passes encrypted traffic to backend | Services with built-in TLS support |

**Enabling External Access:**

```bash
# Enable external access at deploy time
infra deploy --template postgres --set EXPOSE_EXTERNAL=true
```

## Contributing

To add a new template:

1. Create a new directory with the template name
2. Add an `infra-template.yaml` following the specification
3. Add a `README.md` documenting the template
4. Submit a pull request

## License

MIT License - see LICENSE file for details.
