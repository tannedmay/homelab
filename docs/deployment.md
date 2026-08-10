# Deployment

This describes the deployment model conceptually. It does not include actual Compose files, credentials, or infrastructure-identifying details — see [security.md](security.md) for why.

## Flow

```
Git
 ↓
NAS (service configuration)
 ↓
Portainer / Docker Compose
 ↓
Independent service stack
 ↓
Persistent application data
```

Service configuration is version-controlled and lives on the NAS. Each service is deployed as its own Docker Compose stack through Portainer, independent of every other service (see [architecture.md](../architecture/architecture.md) for why stacks are kept separate rather than combined).

## Configuration vs. secrets

Compose definitions describe *structure* — images, volumes, networks, non-sensitive environment variables. Secrets (API tokens, credentials, keys) are never committed alongside that configuration. They're supplied separately at deploy/runtime and excluded from version control entirely (see [security.md](security.md)).

## Persistent data

Each service stack mounts a dedicated volume on the NAS for its application state (documents, database, config), so data outlives container restarts and image updates and stays isolated per service.

## Updates

Updates are applied per stack — pulling a new image and recreating that service's containers — rather than as a single environment-wide upgrade. Because stacks are independent, updating one service carries no risk to the others.

## What's not documented here

The exact scripts, paths, and Portainer setup used day-to-day aren't published. This document describes the pattern, not a runbook that reveals internal infrastructure details.
