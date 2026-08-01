# Incubator — not in the one-click marketplace

These templates are kept but **not seeded into the marketplace**. The seeding
tool only reads template directories at the repository root, so anything here is
excluded automatically; promoting one back is a `git mv` into the root.

## Why a template lives here

The marketplace list answers "what do people most commonly want to run?" — it is
a short, curated set, not an inventory. A template is here because of one or more
of:

- **Niche or enterprise-only** — `db2`, `oracle`, `couchbase`, `aerospike`,
  `scylladb`, `opentsdb`. Real products, rarely what someone reaches for on a
  deploy platform.
- **Needs a datastore it does not ship**, so the one-click promise is false. It
  asks the user for a `DATABASE_URL` / `DB_HOST` they must already have:
  `appsmith`, `authentik`, `directus`, `keycloak`, `linkwarden`,
  `matrix-synapse`, `mattermost`, `nocodb`, `outline`, `plane`, `plausible`,
  `rocketchat`, `sentry`, `sonarqube`, `strapi`, `superset`,
  `umami`, `wikijs`, `zulip`, `harbor`, `hasura`.
- **Cannot work in a Kubernetes pod as configured** — `dozzle` and `traefik`
  both want a Docker socket that does not exist here.
- **Duplicates something already curated** — `keydb`, `dragonfly` (redis),
  `opensearch` (elasticsearch), `typesense` (meilisearch), `timescaledb`,
  `cockroachdb`, `couchdb`, `neo4j`, `cassandra` (postgres/mongo cover the
  common cases), `memcached` (redis), `mssql`.
- **Heavy multi-service platforms** that need a real stack, not a single
  service — `gitlab`, `jenkins`, `drone`, `nextcloud`'s companions.
- **Bundles its whole stack but is too heavy / fragile for one-click** —
  `posthog`. The template ships PostgreSQL, Redis, ClickHouse (with an embedded
  Keeper) and Kafka, wired self-contained, and the Postgres migrations complete
  — but PostHog master's replicated `migrate_clickhouse` does not finish
  creating the ClickHouse event tables on a single node (only the migration-
  history tables appear), and its ~10-minute migrate boot fights the `/_health`
  probe on every restart. Kept as a best-effort reference, not marketplace-ready.
  See its `infra-template.yaml` for the working PG/Redis/ClickHouse/Kafka wiring.

None of this stops anyone deploying them: every one is a public image and the
wizard's **Docker image** source takes any registry reference.

## Promoting a template back

Fix the reason it is here — usually by bundling its dependency as a second
service and wiring it with `ref:`, the way `drupal` bundles its `db` — then
`git mv incubator/<slug> <slug>` and re-seed.
