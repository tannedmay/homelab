# Security

## Remote access

Services are reached over Tailscale, with a reverse proxy sitting in front of the services that need one. This keeps access private by default — devices need to be part of the Tailscale network to reach anything, rather than services being reachable from the open internet.

## Public exposure

Nothing is exposed publicly by default. Any public exposure is deliberate and scoped to a specific service with a specific reason, not a byproduct of default configuration.

## Secrets

Secrets — API tokens, credentials, keys — are intentionally excluded from Git. They're supplied separately at deployment/runtime, never committed alongside service configuration.

## Network separation

The home network uses a dedicated Wi-Fi SSID for IoT devices, separate from the main network. A significant portion of the smart-home environment runs over Zigbee through a coordinator connected to the NAS and managed via Home Assistant, which reduces how much of the IoT footprint touches Wi-Fi at all. This is currently SSID-level separation, not full VLAN-based network segmentation — stated plainly rather than overstated.

## Repository safety

This public repository intentionally excludes:

- Credentials, API keys, and tokens
- Private certificates
- Private domains and hostnames
- Internal IP addresses and network ranges
- Personal documents and application data
- Machine-specific paths and secrets

Everything here is documentation and architecture. No `.env` files, real Compose files, or generated configuration are published — see [deployment.md](deployment.md) for what deployment looks like without exposing those details.
