# KeepRoute 1.0

**OtaconsKeep Stateful AI Orchestration**  
**Designed & Engineered by Antonio G. Garcia (Otaconskeep)**  
**Powered by [OmniRoute](https://github.com/diegosouzapw/OmniRoute)** (upstream routing data plane)

[![KeepRoute Short](https://img.youtube.com/vi/Icc8LA7KIzA/maxresdefault.jpg)](https://youtube.com/shorts/Icc8LA7KIzA)

**[Watch the KeepRoute short on YouTube](https://youtube.com/shorts/Icc8LA7KIzA)** · **[Public page](https://otaconskeep-site.otaconskeep.workers.dev/keeproute/)** · **[Discord](https://discord.gg/cZDeqECzX)**

---

## What this is

**OmniRoute routes a request. KeepRoute owns the mission.**

[OmniRoute](https://github.com/diegosouzapw/OmniRoute) is an excellent upstream gateway: one API in, pick among providers/models, fall back when something is unhealthy. KeepRoute does **not** replace that. KeepRoute is OtaconsKeep’s layer **on top** of OmniRoute — mission control for long agent work.

When Claude hits a limit, a provider fails, Cursor stalls, or the host reboots, stock routing can send the *next* prompt somewhere else. The human still has to rebuild what finished, what failed, and what not to do twice. KeepRoute exists so the **job itself** has an ID, checkpoints, remaining work, and a path to continue on another supported agent.

> **The mission is the product. The model is replaceable.**

## Why it was made

People already had a great way to send chat to many AIs. What kept breaking real Keep workflows was not “pick a model” — it was **losing the job** when that model hit a wall.

| | OmniRoute (upstream) | KeepRoute (OtaconsKeep) |
|---|---|---|
| Job | Route this request | Finish this mission |
| Unit of work | Prompt / response | Mission ID + state |
| On failure | Provider / model fallback | Checkpoint → continue / handoff |
| Ownership | Traffic director | Mission control |

KeepRoute uses OmniRoute underneath for provider/model routing. Without KeepRoute you still have excellent fallback between models. With KeepRoute the mission can survive recoverable failure.

## What KeepRoute adds (1.0)

- Persistent **Mission ID** and mission state
- **Checkpoints** and completed / remaining work tracking
- Recoverable failover with mission-aware continuation
- Supported **cross-agent handoff** (e.g. Claude Code → Codex paths tested in release)
- Service restart and full-host reboot recovery (verified in KeepRoute 1.0 testing)
- Policy-enforced **local-first** routing for trivial work (avoid burning paid tokens on “hi”)
- Field UI (“KeepRoute desk”) for operators

We do **not** claim stock OmniRoute is bad. Capability comparison (not a scoreboard) lives on the [public KeepRoute page](https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#compare).

## Relationship to OmniRoute

```
OtaconsKeep
  └── KeepRoute          ← mission orchestration (this project)
        └── OmniRoute    ← upstream provider/model routing (open source)
```

- Upstream OmniRoute remains its own project: [diegosouzapw/OmniRoute](https://github.com/diegosouzapw/OmniRoute)
- KeepRoute is **not** “our OmniRoute fork” and is **not** stock OmniRoute
- KeepRoute is the OtaconsKeep orchestration / control plane built around OmniRoute

## Install / try it

Self-install (detailed, not one-click) is documented on the public site:

1. Open **[keeproute → Install](https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#install)**
2. Download **KeepRoute-UI.zip**
3. Follow the steps for OmniRoute + the KeepRoute desk UI

Release status, sanitized benchmarks, security notes, pros/cons, and FAQ are on that same page.

## Watch

- Short: https://youtube.com/shorts/Icc8LA7KIzA  
- Embedded next to the “why” write-up: https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#watch

## Release

| Field | Value |
|---|---|
| Product | KeepRoute |
| Version | **1.0** |
| Status | Stable |
| Data plane | OmniRoute (upstream) |
| Builder | OtaconsKeep / Antonio G. Garcia |

## What this repo is

This repository is the **public write-up and product home** for KeepRoute 1.0:

- What it is and why it exists
- How it differs from OmniRoute
- Links to the live page, short, Discord, and install path

Full field deployment, Mission Controller internals, and operator evidence packs live with the OtaconsKeep reference deployment — not as a claim that “clone this repo and you have production Keep.” Use the [public install page](https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#install) for the supported self-install path.

## Links

- Site: https://otaconskeep-site.otaconskeep.workers.dev/keeproute/
- Org: https://github.com/Otaconskeep
- Otacon Core: https://github.com/Otaconskeep/otacons-ai-ecosystem
- OmniRoute (upstream): https://github.com/diegosouzapw/OmniRoute
- Discord: https://discord.gg/cZDeqECzX

---

**ROUTE · RECOVER · CONTINUE // MISSION CONTROL**
