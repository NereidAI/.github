## Introduction 👋

Welcome to **NereidAI** — an end-to-end, self-developed AIoT platform that closes the full loop
from edge data collection, through model training and optimization, to large-scale edge
distribution, edge/cloud inference, and continuous feedback.

NereidAI is delivered as a suite of focused products. Every product can be deployed standalone
as a privatized deployment; together they form one unified platform.

## Product Suite

| Product | What it does |
|---|---|
| **[NereidSphere](/NereidAI/NereidSphere)** | Thin suite layer — one portal, single sign-on, a unified API gateway, and the closed-loop event glue between products. It holds no domain logic of its own. |
| **[NereidLink](/NereidAI/NereidLink)** | Privatized IoT data pipeline — multi-protocol device access, unified messaging, rule engine, alerting, and real-time dashboards. One codebase, two forms: an all-in-one binary or Kubernetes microservices. |
| **[NereidForge](/NereidAI/NereidForge)** | Model factory — the full model lifecycle: dataset and annotation management, training and fine-tuning, optimization and quantization, hardware-targeted export, evaluation, and model registry. |
| **[NereidHorizon](/NereidAI/NereidHorizon)** | Edge distribution and deployment — distributes NereidEdge itself, plus applications and models, to large numbers of remote edge nodes: policy-driven rollout, bandwidth-aware delivery, OTA lifecycle, and autonomous reconciliation while offline. |
| **[NereidEdge](/NereidAI/NereidEdge)** | Southbound edge integration, delivered and kept up to date by NereidHorizon — runs on field gateways, connects field devices into NereidLink with zero-touch onboarding, secure mTLS links, store-and-forward buffering, local autonomy, and OTA lifecycle over intermittent LAN/WAN links. |
| **[NereidFleet](/NereidAI/NereidFleet)** | Robot fleet management — multi-robot task scheduling, traffic coordination, a live operations dashboard, and teleoperation, from wheeled AMRs to legged robots. |

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

Whether you're building real-time video analytics, industrial automation, smart retail, or
robotics operations, NereidAI gives you a scalable, privacy-aware, and cost-effective framework
to bring your AI vision to life.

## Industry Solutions

- **[NereidFactory](/NereidAI/NereidFactory)** — Smart manufacturing: factory operations and intelligent production.
- **[NereidSentinel](/NereidAI/NereidSentinel)** — Guard with Insight, Shape the Future.
- **[NereidMedical](/NereidAI/NereidMedical)** — Empower Healing with Intelligent Insights.

## Our Mission

Democratize AIoT by integrating edge and cloud workflows into one seamless closed loop —
low-latency edge inference, privacy-preserving operations, and automated model monitoring,
all in a single platform.
