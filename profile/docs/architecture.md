# Architecture — How NereidAI Fits Together

Detailed architecture reference for the [NereidAI organization profile](../README.md). The
profile's *How It Fits Together* section stays a short summary and links here; this page owns
the full diagram and its explanations, so they have a stable URL for discussions and proposals.

## Full Architecture Diagram

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
                       Commercial · enterprise-grade · separately licensed
════════════════════════════════════════════════════════════════════════════════════════════════
                           Open source · Apache-2.0 community edition
  ┌─────────────────────────┐    ┌─────────────────────────┐    ┌─────────────────────────┐
  │      NereidIdentity     │◀──▶│       NereidSphere      │◀───│       NereidMobile      │
  │    SSO · auth · RBAC    │    │ portal · gateway · glue │    │app · WeChat mini-program│
  └────────────┴────────────┘    └────────────┴────────────┘    └─────────────────────────┘
 ┊┈┈┈┈┈┈┈┈┈┈┈┈┈┿┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┿
 ┊          ┬─────────────────────────────────┼────────────────────────────────┬
 ┊┌────────────────────┐           ┌────────────────────┐           ┌────────────────────┐
 ┊│    NereidForge     │           │   NereidHorizon    │           │     NereidLink     │
 ┊│   model factory    │  models   │ edge distribution  │           │   device access    │
 ┊│  train · optimize  │──────────▶│   policy rollout   │           │   rules · alerts   │
 ┊│  evaluate · store  │           │   delivery · OTA   │           │     dashboards     │
 ┊│                    │           │                    │           │                    │
 ┊└─────────▲──────────┘           └──────────▼─────────┘           └──────────▲─────────┘
 ┊          └◀──── data ──────────────────────┼────────────────────────────────┤
┈┊┈┈┈┈ WAN · intermittent links ┈┈┈┈┈┈┈┈┈┈┈┈┈┈┴┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┼┈┈┈┈┈┈┈┈┈┈
 ┊        telemetry: secure mTLS · store-and-forward                           │
 ┊SSO · portal · mgmt over WAN                distributes NereidEdge + apps / models (OTA)
 ┊                          Site / field · local LAN (low latency)             │
 ┊┌──────────────────┐ commands ┌────────────────┐                  ┌──────────┴─────────┐
 ┊│   NereidFleet    │─────────▶│  NereidRobot   │                  │     NereidEdge     │
 └│ task scheduling  ◀───────── │  AMR · legged  │                  │ zero-touch · mTLS  │
 ┊│ traffic control  │    state │   + robot DS   │                  │ store-and-forward  │
 ┊│dashboard · teleop│          │                │────── data ─────▶│   local autonomy   │
 ┊└──────────────────┘          └────────────────┘                  └──────────┬─────────┘
   real-time low-latency LAN control loop       models ↓ · events ↑            │
                                                                               │
                        ┌────────────────────────────────────────────────┬─────┘
                        │                                                │
 ┊       ╭╌╌╌╌╌╌╌╌╌╌╌╌╌╌┬╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╮                 ┌──────────────┬───────────────┐
 ┊       ┆ EdgeX Core + Device Services ┆                 │       NereidInference        │
 ┊       ┆    onvif · mqtt · modbus     ┆                 │      camera · KServe v2      │
 ┊       ┆        rest · opc-ua         ┆                 │      decode → EdgeX bus      │
 ┊       ╰╌╌╌╌╌╌╌╌╌╌╌╌╌╌┬╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╯                 └──────────────▲───────────────┘
             data ↑     │                                     frames ↑   │
                        │                                                │
         ╭╌╌╌╌╌╌╌╌╌╌╌╌╌╌┬╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╮                 ╭╌╌╌╌╌╌╌╌╌╌╌╌╌╌┬╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╮
         ┆     PLC · meters · RFID      ┆                 ┆      sensors · cameras       ┆
         ┆        field devices         ┆                 ┆        field gateways        ┆
         ╰╌╌╌╌╌╌╌╌╌╌╌╌╌╌┴╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╯                 ╰╌╌╌╌╌╌╌╌╌╌╌╌╌╌┴╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╯
  ──▶ data / artifacts / telemetry · feedback loop
  ▼▲ real-time low-latency LAN link (commands / state)
  ┈▶ SSO · portal · management plane over WAN
  ◀ NereidMobile (app · WeChat Mini Program): telemetry · charts · alerts via NereidLink
  ▼ NereidEdge itself, apps and models distributed by NereidHorizon (OTA)
  ◆ device-inference (NereidInference): frames → KServe v2/OpenAI → EdgeX events
  ╭╌╌╮ dashed box = external: upstream EdgeX core & device services, physical devices
```

Each product owns its domain end to end — its own UI, APIs, and release cycle. The suite layer is
deliberately thin: NereidSphere provides the common portal, API gateway, and cross-product event
glue, while NereidIdentity owns authentication and access control. Neither duplicates a product's
features, and both are optional for single-product deployments.

NereidMobile is the thin client at the edge of the diagram: a mobile app and WeChat Mini Program
that authenticates through NereidIdentity, reads device telemetry and dashboards from NereidLink,
and delivers alerts — without running any domain logic of its own.

NereidInference is the edge inference runtime: an EdgeX device service on each field gateway,
it acquires camera frames (RTSP / ONVIF / JPEG), calls standards-based model servers
(OpenVINO Model Server, Triton, vLLM, MindIE) over KServe gRPC v2 or OpenAI-compatible HTTP,
decodes the raw tensors according to vendor-neutral model package descriptors, and publishes
structured events back onto the EdgeX message bus. It contains no vendor SDK and no business
logic — model packages are built by NereidForge and delivered by NereidHorizon.
