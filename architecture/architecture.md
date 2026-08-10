# Architecture

## Machines

**Orion — Mac mini**
Local AI and development machine. Runs Ollama for local LLM inference, OpenClaw, and AI-assisted development workflows (Aider and similar tooling). Nothing here is expected to be highly available — it's used interactively.

**Alex — Synology NAS**
The always-on infrastructure machine. Runs Docker and Portainer, and hosts the persistent self-hosted services: Paperless-ngx, Home Assistant, SearXNG, and other independent stacks. This is the machine that needs to be up continuously, since it holds application state and runs services other things depend on (e.g., Home Assistant's Zigbee coordinator).

## Why split workloads across two machines

The split follows what each machine is actually good at, not an arbitrary preference:

- The Mac mini is the better fit for local AI/development work — it's the machine I'm actively using, and interactive workloads (running a model, iterating with an AI coding assistant) don't need 24/7 uptime.
- The NAS is built for persistent storage and always-on operation. Self-hosted services that need to be reachable at any time — document management, home automation, search — belong there, not on a machine that gets put to sleep or rebooted for development work.
- Keeping AI/dev workloads off the NAS also means experimenting on Orion (new models, new tooling) can't take down a service I depend on daily, like Home Assistant.

This isn't a performance-driven split with benchmarks behind it — it's a reliability and blast-radius decision.

## Service architecture

Each service on Alex runs as its own independent Docker Compose stack rather than all services living in one large Compose file. Reasons:

- **Independent lifecycle** — each service can be updated, restarted, or rebuilt without touching anything else.
- **Fault isolation** — a misconfigured or crashing service stays contained to its own stack instead of taking down unrelated services.
- **Simpler troubleshooting** — when something breaks, the blast radius is one stack's logs and config, not a monolithic file shared by everything.
- **Reduced blast radius on change** — pulling a new image or changing an environment variable for one service can't accidentally affect another.

The tradeoff is a bit more repetition across stacks (shared patterns like reverse proxy labels or network config are duplicated rather than centralized). For a single-operator homelab, that repetition is worth the isolation.
