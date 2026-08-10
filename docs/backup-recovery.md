# Backup & Recovery

## Data categories

Three categories of data exist in this environment, and they're treated differently:

```
Recreatable configuration
        ↓
        Git

Persistent application data
        ↓
      Backup

Secrets
        ↓
Separate secret storage
```

**Recreatable configuration** — Compose stack definitions and structural config. If lost, this can be rebuilt from Git and redeployed.

**Persistent application data** — the actual state services accumulate: Paperless documents and its database, Home Assistant configuration and history, and similar per-service data. This lives on the NAS, under the Docker/service storage used by each stack.

**Secrets** — credentials, tokens, keys. Kept out of Git entirely and supplied at deploy/runtime (see [security.md](security.md)).

## Current backup coverage

The NAS's Docker/service storage — which is where persistent application data lives — is backed up to an external disk. So data for services like Paperless and Home Assistant is covered by that backup, provided it resides under the backed-up folder.

This protects against loss or failure of the primary NAS storage. It has **not** been validated with an actual end-to-end restore test, so I'm not claiming a proven disaster-recovery process — only that a backup of the underlying data exists.

## What a rebuild would conceptually look like

1. Redeploy service stacks from the Compose configuration in Git.
2. Restore persistent application data from the external-disk backup into each service's volume.
3. Reintroduce secrets separately (not from Git, not from the backup).

## Honest gaps

- No automated restore testing exists yet — the backup's recoverability is assumed, not proven.
- Backup coverage depends on data actually living under the backed-up folder; this isn't independently audited per service.
- There's no offsite/3-2-1 backup — the external disk is the only backup target today.

This is a known area for improvement, not a claimed strength.
