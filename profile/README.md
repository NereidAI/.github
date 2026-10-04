## Introduction 👋

**NereidAI** is an end-to-end AIoT platform built for air-gapped and multi-vendor silicon
environments.

It closes the loop from edge data collection → multi-silicon model factory → large-scale offline
edge distribution → inference and feedback — without mandatory cloud dependency.

NereidAI ships as a suite of focused products; each can be deployed standalone, and together
they form one unified platform.

- **Private by design** — fully offline / air-gapped delivery, no mandatory call-home; built for
  sovereign, regulated, and network-isolated environments
- **Multi-silicon model factory** — one train → quantize → export → deploy → evaluate pipeline
  across NVIDIA, AMD, Intel, ARM, Ascend, SOPHGO, MetaX, Enflame, and Rockchip
- **Production proven** — core components already operate in private production deployments
  (NDA references on request)
- **Commercially supported** — community edition is open source; enterprise LTS, SLAs, hardening,
  and turnkey private delivery are available

> The community edition of the full edge-to-cloud loop is planned for open source by the end
> of 2026. Individual modules will be released progressively; status will be tracked in this
> organization and in Discussions. Enterprise capabilities are offered commercially
> (see [Community vs Enterprise](#community-vs-enterprise)).

## Team & Open-Source Governance

NereidAI is built by open-source insiders, not outsiders replacing the open-source edge stack:

- **[EdgeX Foundry](https://www.edgexfoundry.org/ecosystem/leadership/)** — our lead has
  served on the [**Technical Steering Committee**](https://lf-edgexfoundry.atlassian.net/wiki/spaces/FA/pages/11667441/Technical+Steering+Committee+TSC)
  **since 2024**, contributing modules and features now running in production across numerous
  projects. NereidEdge is an enterprise-grade edge integration layer built *on top of* EdgeX,
  tracking upstream rather than forking it.
- **Open Horizon** — we authored the **Open Horizon Web UI** contribution, to be released to
  the community in Q4 2026; NereidHorizon is our enhancement around it.
- **Multi-vendor GPU/NPU expertise** — deep hands-on integration across NVIDIA, AMD, Intel,
  ARM, SOPHGO (算能), MetaX (登临), Enflame (灵汐), Rockchip (瑞芯微), and Ascend (华为昇腾),
  including early-silicon and vendor certification work.
- **Advocacy** — Intel Global Innovation Agent & ARM Innovation Ambassador, Huawei Geek
  Developer, and a multi-time winner of Huawei developer competitions (2022, 2024, 2025).

## Deployment & Security Posture

| Capability | Status |
|---|---|
| Air-gapped / offline installation (full image bundles, no internet required) | Available |
| No mandatory call-home; data stays inside the customer perimeter | Available |
| mTLS service-to-service and device-to-platform links | Available |
| Signed release artifacts and per-release SBOM | Planned |
| Enterprise SLA on CVE response and backported fixes | Commercial contract |
| Compliance baselines (e.g. 等保 2.0) and local-OS / accelerator certifications | Per-engagement roadmap |

## Product Suite

| Product | What it does |
|---|---|
| **NereidSphere** | Thin suite layer — one portal, single sign-on, a unified API gateway, and the closed-loop event glue between products. It holds no domain logic of its own. |
| **NereidLink** | Privatized IoT data pipeline — multi-protocol device access, unified messaging, rule engine, alerting, and real-time dashboards. One codebase, two forms: an all-in-one binary or Kubernetes microservices. |
| **NereidForge** | Model factory — dataset/annotation management, training and fine-tuning, optimization and quantization, hardware-targeted export across the silicon vendors above, evaluation, and model registry. |
| **NereidHorizon** | Edge distribution and deployment — distributes NereidEdge itself, plus applications and models, to large numbers of remote edge nodes: policy-driven rollout, bandwidth-aware delivery, OTA lifecycle, and autonomous reconciliation while offline. |
| **NereidEdge** | Southbound edge integration, delivered and kept up to date by NereidHorizon — runs on field gateways, connects field devices into NereidLink with zero-touch onboarding, secure mTLS links, store-and-forward buffering, local autonomy, and OTA lifecycle over intermittent LAN/WAN links. |
| **NereidFleet** | Robot fleet management — multi-robot task scheduling, traffic coordination, a live operations dashboard, and teleoperation, from wheeled AMRs to legged robots. |

## How It Fits Together

```
                            Industry applications · domain solutions
┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
      ┌──────────────────────┐      ┌──────────────────────┐      ┌──────────────────────┐
      │    NereidFactory     │      │    NereidSentinel    │      │    NereidMedical     │
      │ smart manufacturing  │      video & image analytics│      │   medical insights   │
      └──factory operations──┘      └──────────────────────┘      └──────────────────────┘
                  │                           │                               │
                  │      consume platform via portal · SSO · gateway          │
                  ┬───────────────────────────┼───────────────────────────────┬
                             Private platform · cloud / central DC
════════════════════════════════════════════════════════════════════════════════════════════════
                              ┌──────────────────────────────┐
                              │         NereidSphere         │
                              │portal · SSO · gateway · glue │
                              └───────────────┴──────────────┘
 ┊┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┿
 ┊          ┬─────────────────────────────────┼────────────────────────────────┬
 ┊┌────────────────────┐           ┌────────────────────┐           ┌────────────────────┐
 ┊│     NereidLink     │           │    NereidForge     │           │   NereidHorizon    │
 ┊│   device access    │data ───▶  │   model factory    │artifacts  │ edge distribution  │
 ┊│   rules · alerts   │◀─ feedback│  train · optimize  │──────────▶│   policy rollout   │
 ┊│     dashboards     │           │  evaluate · store  │           │   delivery · OTA   │
 ┊│                    │           │                    │           │                    │
 ┊└─────────▲──────────┘           └────────────────────┘           └──────────▼─────────┘
 ┊          └──────────────────────────────────────────────────────────┐       │
┈┊┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈  WAN · intermittent links  ┈┈┈┈┈┈┈┈┈┼┈┈┈┈┈┈┈┼┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈
 ┊        telemetry: secure mTLS · store-and-forward                   │       │
 ┊  SSO · portal · mgmt over WAN              distributes NereidEdge + apps / models (OTA)
 ┊                           Site / field · local LAN (low latency)    │       │
 ┊┌──────────────────┐ commands ┌────────────────┐                  ┌────────────────────┐
 ┊│   NereidFleet    │─────────▶│     robots     │                  │     NereidEdge     │
 └│ task scheduling  ◀───────── │  AMR · legged  │                  │ zero-touch · mTLS  │
  │ traffic control  │   state  │                │                  │ store-and-forward  │
  │dashboard · teleop│          │                │                  │   local autonomy   │
  └──────────────────┘          └────────────────┘                  └─────────▼──────────┘
    real-time low-latency LAN control loop                                       cmds / data
                                                                    ┌─────────▲──────────┐
                                                                    │ sensors · cameras  │
  ──▶ data / artifacts / telemetry · feedback loop                  │   field gateways   │
  ▼▲ real-time low-latency LAN link (commands / state)              └────────────────────┘
  ┈▶ SSO · portal · management plane over WAN
  ▼ NereidEdge itself, apps and models distributed by NereidHorizon (OTA)
```

Each product owns its domain end to end — its own UI, APIs, and release cycle. NereidSphere only
provides a common entry point and federates identity; it never duplicates a product's features.

## Community vs Enterprise

The community edition covers the complete edge-to-cloud loop and can be used in production on
its own. Enterprise capabilities cover the procurement, operations, and compliance needs of
large organizations.

| Capability | Community | Enterprise value |
|---|---|---|
| NereidEdge connectors & NPU adapter plugins | ✓ | Same capabilities, with vendor-backed integration support |
| NereidLink / NereidForge / NereidHorizon, single-site | ✓ | Multi-site scale — federated operation across many sites, certified hardware/accelerator combinations, and vendor-supported rollout with LTS |
| NereidSphere multi-tenant suite control plane | — | Unified identity and cross-product governance — one login, one portal, consistent tenants and policies across the suite |
| High availability, disaster recovery, large-scale rollout policy | — | Scale and resilience — fleet-wide rollouts with HA/DR for sites that cannot tolerate downtime |
| Audit, hardening, compliance baselines (等保 etc.) | — | Compliance and audit readiness for regulated industries and SOE / multinational procurement |
| Signed artifacts, SBOM, CVE SLA, backported fixes | community | Supply-chain security and a contractual response commitment |
| Air-gapped delivery, private POC, training | self-service | Turnkey delivery and knowledge transfer on the customer's premises |
| Support | GitHub issues / Discussions | 7×24 LTS with a named engineer |

Licensing of the community components will be confirmed as the modules are open-sourced
through 2026.

## Industry Solutions

- **NereidFactory** — Smart manufacturing: factory operations and intelligent production.
- **NereidSentinel** — Guard with Insight, Shape the Future.
- **NereidMedical** — Empower Healing with Intelligent Insights.

## Our Mission

Democratize AIoT by integrating edge and cloud workflows into one seamless closed loop —
low-latency edge inference, privacy-preserving operations, and automated model monitoring,
all in a single platform.

## Contact

For enterprise inquiries, NDA references, or collaboration: please reach out through the
**[NereidAI organization on GitHub](https://github.com/NereidAI)** (organization owners /
Discussions).
